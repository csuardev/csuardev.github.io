---
title: "{{ replace .Name "-" " " | title }}"
author: ""
type: ""
date: {{ .Date | time.Format ":date_long" }}
subtitle: ""
image: ""
tags: []
---
