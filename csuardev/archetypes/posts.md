+++
title = '{{ replace .File.ContentBaseName "-" " " | title }}'
subtitle: ""
draft = true
author: ""
description: ""
date: {{ .Date | time.Format ":date_long" }}
image: ""
tags: []
+++