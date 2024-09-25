---
title: 'Write to Google Sheets from a local script via gcloud CLI authentication'
date: '2024-09-25T17:48:17Z'
publishDate: '2024-09-25T17:48:17Z'
# Description is used as a page subtitle & search index.
description: 'Report & share data from a local script via a Google Sheet without implementing complicated authentication.'

categories:
  - Post
tags:
  - googlecloud
  - googlesheets
  - authentication
  - automation
  - gcloud
  - oauth

draft: false # hide from production deployment
hidden: false # hide from lists if true
rssFullContent: true

# Specify an external canonical URL.
# If both are present, will be linked from footer of post.
# canonical: http://example.com
# canonicalTitle: Example Article Title

image: cover.jpg

imageCredit: |
  Cover Photo by <a href="https://unsplash.com/@albovsky?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Glib Albovsky</a> on <a href="https://unsplash.com/photos/an-aerial-view-of-a-green-field-ClSDm_cFdOo?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Unsplash</a>

# context: Nothing today.
---

Recently I needed to pull data from the GitHub API and publish to a Google Sheet
so I could share some charts about code review workload. This post is about how
I got authentication working.

I wrote a Node.js script because my use case was too complex for BASH and
seemed too temporary for Go. Initially, the script generated ad hoc CSV data
that I could manually copy into Google Sheets using the Paste-as-CSV feature.
After a few rounds of manually copying, I wanted to use my time wisely: I
estimated that I could get a Sheets integration working within a couple hours
and if so that would
[probably pay off within a few months](https://dev.to/grayside/why-automate-1fac).

{{< callout important >}}
**Aside:** Sheets isn't my database. It's my data reporting UI.
*Don't use Sheets as your database.*
{{</ callout >}}

It took me closer to 4 hours to get this working, because authentication is
hard. This post shows the more direct path to a working solution.

It's easy to be distracted with the complexity of
[creating an oAuth application](https://developers.google.com/workspace/guides/configure-oauth-consent),
and while I especially like
[using a service account](https://developers.google.com/workspace/guides/create-credentials#service-account)
from my Cloud services, that's
[less convenient when running locally](https://cloud.google.com/sdk/docs/authorizing#service-account).

The trick I learned is that the gcloud CLI's setup of
[application default credentials](https://cloud.google.com/docs/authentication/application-default-credentials) can
operate as a sort of OAuth proxy for Google Workspace code, by expanding how it
authenticates your account with Google to include some more permissions
(OAuth Scopes).

### 🔐 Local authentication with gcloud CLI

To make API requests to Google Sheets, enable the Sheets API in your Cloud project:

```sh
$> gcloud services enable sheets.googleapis.com

Operation "operations/acat.p2-480745230567-02564c8d-c6ba-4f60-90bd-13f33e41f0fe" finished successfully.
```

Set your **application default credentials**, also claiming some non-default OAuth scopes so the credential can be used with sheets:

```sh
$> gcloud auth application-default login --scopes \ 'https://www.googleapis.com/auth/cloud-platform,https://www.googleapis.com/auth/drive,https://www.googleapis.com/auth/spreadsheets'
```

This will trigger an OAuth flow that involves visiting a webpage in your browser. Depending on the terminal configuration this may display a URL or even open the page.

{{< callout info >}}When gcloud is granted this scope, code that can access your local default application credentials will have read/write access to all your Google Sheets data. You can re-run this command without the customized scopes to toggle that access on and off as needed.
{{</callout>}}

### 🗝️ Initialize a Node client for Google Sheets

```js
import {google} from 'googleapis';

function sheetsClient() {
    const authConfig = new google.auth.GoogleAuth({
        scopes: [
            // Only 'spreadsheets' scope is needed in the code.
            // gcloud CLI also needs 'cloud-platform' and 'drive'.
            'https://www.googleapis.com/auth/spreadsheets'
        ],
    });
    const auth = await authConfig.getClient();
    return google.sheets({version: 'v4', auth});
}
```

### 🖇 Append data to the sheet

The sample from the docs on how to
[append data to the sheet](https://developers.google.com/sheets/api/guides/values#append_values)
(click on Node.js tab) worked well. However, I couldn't understand how to get
authentication working from there. The sample worked once I understood the
trick above to add the missing OAuth scopes to my dev environment credentials.

I made a few changes to enable easier reuse of the client, parameterize the
request differently, and emphasize how I wanted the data handled by Sheets.

My code to append data to the sheet, leveraging the client initialization code above:

```js
let client;

async function appendDataToSheet(spreadsheetId, tab, values) {
    if (!client) {
        client = sheetsClient();
    }

    try {
        const result = await client.spreadsheets.values.append({
            spreadsheetId,
            range: `${tab}!A2:AG`,
            // Use my data as provided.
            valueInputOption: 'RAW',
            // Inserts rows as part of appending to reduce overwrites.
            insertDataOption: 'INSERT_ROWS',
            requestBody: { values },
        });
        console.log(`${result.data.updates.updatedCells} cells appended.`);
    } catch(e) {
        // Show the error, do not stop. Cross-reference the error with terminal output
        // and decide case-by-case to re-run the script or manually copy data.
        console.error(e);
    }
}
```

I'm using "append" because my script collects monthly metrics, and append allows me to add new rows without removing earlier rows.

Here's an example of how to call the `appendDataToSheet()` function:

```js
const values = [
  // Each nested array is a spreadsheet row.
  [1, 2, 3, 4, 'luggage'],
  [4, 5, 6, 'N/A', 'sticks'],
];
appendDataToSheet('HPDkfqdu6rfIq5-4uTGDqz2tvmPxDZMul27JFexample', 'Exported Data Tab', values);
```

There are some good tips about working with the
[sheets API in the docs](https://developers.google.com/sheets/api/troubleshoot-api-errors)
such as the suggestion not to send more than one API request per second per
sheet. I found out the hard way: overwriting data from a first request with
data from a second.


### 🚀 Ship it!

If I move forward to productionize this, I might switch to using
[Cloud Scheduler](https://cloud.google.com/scheduler/docs) and
[Cloud Run Jobs](https://cloud.google.com/run/docs/quickstarts/jobs/build-create-nodejs).
Let me know if you'd like to read about that.
