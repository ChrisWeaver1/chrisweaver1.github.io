---
layout: md
title: Python SMTP debugging
description: Quick script to help debug SMTP issues
tags: ['python', 'smtp']
sort_key: 1
permalink: blog/2020-07-21-encrypted-usb-share
---

{% include project-headers.html %}

```python
import smtplib, ssl
import poplib, ssl
from email.message import EmailMessage

port = 465  # For SSL
password = ""

# Create a secure SSL context
context = ssl.create_default_context()
msg = EmailMessage()
msg.set_content('helo, this is a debug email')
msg['Subject'] = 'debugging email'
msg['To'] = ''
msg['From'] = ''

with smtplib.SMTP_SSL("", port, context=context) as server:
    server.set_debuglevel(2)
    res = server.login("", password)
    print(res)
    res = server.send_message(msg)
    print(res)
```
