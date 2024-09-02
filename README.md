# Concourse Blog

This repo contains the blog posts for blog.concourse-ci.org

## Running the blog locally

```
hug server
```

## Adding a New Blog Post

```
touch content/posts/YYYY-MM-DD-my-post.md
```
With the following content
```
+++
title = "{{ replace .TranslationBaseName "-" " " | title }}"
date = "{{ .Date }}"
author = ""
tags = ["", ""]
keywords = ["", ""]
description = ""
+++
```