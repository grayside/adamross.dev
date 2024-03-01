---
title: '{{ replace .File.ContentBaseName `-` ` ` | title }}'
date: '{{ .Date }}'
publishDate: '{{ .Date }}'
# Description is used as a page subtitle & search index.
description: 'Some description, huh?'

categories:
  - TODO

tags:
  - TODO

draft: true # hide from production deployment
hidden: false # hide from lists if true
rssFullContent: true

# Specify an external canonical URL.
# canonical: http://example.com
---
