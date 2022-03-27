---
layout: post
title: "GitHub OpenID with Python for AWS CDK"
author: "John Lukach"
tags: AWS CDK GitHub OpenID
---

I have started using GitHub Actions to trigger a few workflows in my AWS environment where I wanted to set up an OpenID connection to get out of credential management.

[https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/configuring-openid-connect-in-amazon-web-services](https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/configuring-openid-connect-in-amazon-web-services)

I did not see a Python example using Amazon Web Services (AWS) Cloud Development Kit (CDK); thus wanted to share a code snippet to accomplish this task. Remember to follow the practice of least privilege for any permissions you attach to this role.

```python
provider = _iam.OpenIdConnectProvider(
	self, 'provider',
    url = 'https://token.actions.githubusercontent.com',
    client_ids = [
    	'sts.amazonaws.com'
    ]
)

role = _iam.Role(
	self, 'role',
		assumed_by = _iam.WebIdentityPrincipal(
        	provider.open_id_connect_provider_arn
        ).with_conditions(
        {
            'StringLike': {
                'token.actions.githubusercontent.com:sub': 'repo:4n6ir/matchmeta.info:*'
            }
        }
    )
)
```

Finally, here is how to access the AWS Role with a GitHub Action from a push event. I am using a GitHub Secret, so the IAM Role ARN is not disclosed.

```yaml
name: AWS Authenication
on:
  push:
    branches: [ main ]
jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      id-token: write
      contents: read
    steps:
      - name: AWS Credentials
        uses: aws-actions/configure-aws-credentials@v1
        with:
          role-to-assume: ${{ secrets.ROLEARN }}
          aws-region: us-east-2
```