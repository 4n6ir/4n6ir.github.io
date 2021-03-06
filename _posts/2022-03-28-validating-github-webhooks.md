---
layout: post
title: "Validating GitHub Webhooks with Python"
author: "John Lukach"
tags: GitHub Python Webhooks
---

I added a GitHub Webhook that invalidates the CloudFront Distribution using a Tag increment with the published Release that I needed to secure.

[https://docs.github.com/en/developers/webhooks-and-events/webhooks/securing-your-webhooks](https://docs.github.com/en/developers/webhooks-and-events/webhooks/securing-your-webhooks)

List of Python libraries necessary for the code example to work.

```python
import hashlib
import hmac
import re
```

Parse the SHA256 verification hash from the Lambda Function that received the GitHub Webhook event from the API post request.

```python
webhook = re.sub(r'^sha256=', '', event['headers']['X-Hub-Signature-256'])
```

Calculate the Hash-Based Message Authentication Code (HMAC) using the GitHub Webhook Secret and Event Body in a UTF-8 format to generate a SHA256 hash.

```python
digest = hmac.new(
    key = bytes(secret, 'utf-8'),
    msg = event['body'].encode('utf-8'),
    digestmod = hashlib.sha256
)
```

Two matching values indicate a successful GitHub Webhook authentication.

```python
if webhook != digest.hexdigest():
    print('Denied Access')
else:
    print('Access Granted')
```
