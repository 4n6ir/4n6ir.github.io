---
layout: post
title: "Route53 Public Host Zone Pattern"
author: "John Lukach"
tags: AWS Route53 SPF DMARC DKIM DNSSEC
---

It's always DNS (Domain Name System), thus wanted to document a pattern for a Route53 Public Host Zone deployed in AWS (Amazon Web Service) with CDK (Cloud Deployment Kit) using the Python flavor.

The first thing is; first, query logging needs to be enabled, or it didn't happen! To accomplish this requirement, I must provide a Cloud Watch (CW) Log Group with a Resource Policy with permissions for the Route53 service to create streams and put events for the logs.

```
account = Stack.of(self).account
region = Stack.of(self).region
        
policy_statement = _iam.PolicyStatement(
    principals = [
        _iam.ServicePrincipal('route53.amazonaws.com')
    ],
    actions = [
        'logs:CreateLogStream',
        'logs:PutLogEvents'
    ],
    resources=[
        'arn:aws:logs:'+region+':'+account+':log-group:*'
    ]
)

resourcepolicy = _logs.ResourcePolicy(
    self, 'resourcepolicy',
    policy_statements = [
        policy_statement
    ],
    resource_policy_name = 'AWSServiceRoleForRoute53'
)

logs = _logs.LogGroup(
    self, 'logs',
    log_group_name = '/aws/route53/fileblock.info',
    retention = _logs.RetentionDays.INFINITE,
    removal_policy = RemovalPolicy.DESTROY
)

hostzone = _route53.PublicHostedZone(
    self, 'hostzone', 
    zone_name = 'fileblock.info',
    comment = 'https://github.com/jblukach/fileblockinfo',
    query_logs_log_group_arn = logs.log_group_arn
)
```

I always add a Sender Policy Framework (SPF) record to all my domains, even if a Mail Exchanger (MX) record is not present. I do this to notify the Internet that no emails will be sent from this domain to help protect against spoofing.

 ```
 spf = _route53.TxtRecord(
    self, 'spf',
    zone = hostzone,
    values = ['v=spf1 -all'],
    ttl = Duration.minutes(300)
)
```

I also added a Domain-based Message Authentication Reporting and Conformance (DMARC) record without a Domain Keys Identified Mail (DKIM) record in strict mode to help defend the domain's reputation. 

```
dmarc = _route53.TxtRecord(
    self, 'dmarc',
    zone = hostzone,
    record_name = '_dmarc',
    values = ['v=DMARC1; p=reject; aspf=s; adkim=s;'],
    ttl = Duration.minutes(300)
)
```

Finally, I enable Domain Name System Security Extensions (DNSSEC) to add authentication and integrity to the resolution requests.

```
key = _kms.Key(
    self, 'key',
    key_spec = _kms.KeySpec.ECC_NIST_P256,
    key_usage = _kms.KeyUsage.SIGN_VERIFY,
    removal_policy = RemovalPolicy.DESTROY
)

key.add_alias('alias/fileblockinfo')

key_policy_one = _iam.PolicyStatement(
    effect = _iam.Effect(
        'ALLOW'
    ),
    actions = [
        'kms:DescribeKey',
        'kms:GetPublicKey',
        'kms:Sign'
    ],
    principals = [
        _iam.ServicePrincipal('dnssec-route53.amazonaws.com')
    ],
    resources = [
        '*'
    ]
)

key.add_to_resource_policy(
    statement = key_policy_one
)

key_policy_two = _iam.PolicyStatement(
    effect = _iam.Effect(
        'ALLOW'
    ),
    actions = [
        'kms:CreateGrant'
    ],
    principals = [
        _iam.ServicePrincipal('dnssec-route53.amazonaws.com')
    ],
    resources = [
        '*'
    ]
)

key_policy_two.add_condition('Bool', {'kms:GrantIsForAWSResource': 'true'})

key.add_to_resource_policy(
    statement = key_policy_two
)

ksk = _route53.CfnKeySigningKey(
    self, 'ksk',
    hosted_zone_id = hostzone.hosted_zone_id,
    key_management_service_arn = key.key_arn,
    name = 'fileblockinfo',
    status = 'ACTIVE'
)

dnssec = _route53.CfnDNSSEC(
    self, 'dnssec',
    hosted_zone_id = hostzone.hosted_zone_id
)
```