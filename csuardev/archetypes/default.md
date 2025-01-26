+++
date = '{{ .Date | time.Format ":date_long"}}'
draft = true
title = '{{ replace .File.ContentBaseName "-" " " | title }}'
+++
