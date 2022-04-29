---
layout: post
title: "Privileged Account Identification"
author: "John Lukach"
tags: AWS Delegated Organization
---

A best practice is always having your contact information updated in your AWS Accounts if AWS Security needs to contact you for problems in paradise. I recently split Billing, Operations, and Security contact addresses into unique emails for easier identification. 

On February 9th, Amazon released a new feature that allows for centralized configuration of contact information from the AWS Organizations console in the management account.  

I enabled the trust access for AWS Account Management that allows for cross-account contact configuration with the new feature. The delegated administration for this service does not appear in the AWS Console as only available by CLI & SDKs.    

```
aws organizations register-delegated-administrator \
    --account-id 123456789012 \
    --service-principal account.amazonaws.com
```

Granting privileged access to the organization endpoint through delegated administration is better than punching IAM permissions but could still be abused for malicious intent. Another risk is having your contact information misrepresented so AWS Security cannot get ahold of you, extending the time to detection. An SCP to prevent this risk is a foundational building block that should be a default configuration.

[https://docs.aws.amazon.com/accounts/latest/reference/using-orgs-example-scps.html](https://docs.aws.amazon.com/accounts/latest/reference/using-orgs-example-scps.html)

There is an entire list of services with Delegated Administration capabilities. 

 - AWS Account Management
 - AWS Audit Manager
 - AWS CloudFormation Stacksets
 - AWS Config
 - Amazon Detective
 - Amazon DevOps Guru
 - AWS Firewall Manager
 - Amazon GuardDuty
 - IAM Access Analyzer
 - Amazon Inspector
 - AWS License Manager
 - Amazon Macie
 - AWS Security Hub
 - Amazon S3 Storage Lens
 - AWS Service Catalog
 - AWS Systems Manager
 - Amazon VPC IP Address Manager (IPAM)

[https://docs.aws.amazon.com/organizations/latest/userguide/orgs_integrate_services_list.html](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_integrate_services_list.html)

How do we identify privileged AWS accounts? In the management (root) account or an existing delegated administrator account for the organization, run the following AWS CLI command.

```
aws organizations list-delegated-administrators
```

The following command finds delegated service principals from the identified accounts. 

```
aws organizations list-delegated-services-for-account \
	--account-id 123456789012
```

After digging into this new feature, I stuck with the least privilege and used the API calls to configure my contact information for BILLING, OPERATIONS, and SECURITY.

```
aws account put-alternate-contact \
	--alternate-contact-type=SECURITY \
	--email-address=<value> \
	--name="<value>" \
	--phone-number="<value>" \ 
	--title="<value>"
```
