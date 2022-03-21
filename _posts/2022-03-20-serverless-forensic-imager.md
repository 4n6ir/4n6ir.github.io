---
layout: post
title: "Serverless Forensic Imager"
author: "John Lukach"
tags: AWS EBS Python Serverless
---

##### ORIGINALLY

I initially released my **Snapshot 4n6ir Imager** to simplify the Elastic Compute Cloud (EC2) forensic acquisition of Elastic Block Storage (EBS) volumes from Snapshots using the EBS Direct API that focused on missing data security encryption requirements. 

The EBS Direct API released at AWS re:Invent 2019 did not initially have any Cloud Trail logging creating a risk of undetected data exfiltration. It was always safer to just put a Service Control Policy (SCP) on it!

```json
{    
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "EBSDirectAPI",
      "Effect": "Deny",
      "Action": "ebs:*",
      "Resource": "*"
    }
  ]
}
```

##### AWS IMPROVEMENTS

Mid-Summer 2020, EBS Direct API Virtual Private Cloud (VPC) Endpoint preview was sporadically available for testing. In November of 2020, Amazon Web Services (AWS) released VPCE for EBS, allowing for connectivity from an isolated subnet to guarantee the data never left the network segment. 

```
com.amazonaws.us-east-2.ebs
```

AWS released the ability to re-encrypt a snapshot using Key Management Service (KMS) at re:Invent 2020, obsoleting many of the script's capabilities with a few considerations.

 1. Encrypted snapshots can not become unencrypted
 2. Unencrypted snapshots are optional for encryption
 3. Shared encrypted snapshots need KMS permissions

In July of 2021, Cloud Trail gained the ability to log data events for EBS Direct API under the advanced selector providing the necessary monitoring and detection capabilities. 

 - ebs:ListSnapshotBlocks
 - ebs:ListChangedBlocks
 - ebs:GetSnapshotBlock
 - ebs:PutSnapshotBlock

These data events are still not logged by default; thus wise to leave the SCP in place. Also, shared snapshots do not send the data events to the AWS account that owns the snapshot.

![Cloud Trail Data Event EBS Direct API](/images/2022/03/dataevent-ebsdirectapi-cloudtrail.jpg)

##### SNAP4N6 UPDATES

First thing, first, I renamed **Snapshot 4n6ir Imager** to **Snap4n6**.

Next, I switched the script to a serverless solution for less maintenance.

[https://github.com/jblukach/snap4n6](https://github.com/jblukach/snap4n6)

Finally, I created a Python package for rebuilding the forensic image.

[https://pypi.org/project/snap4n6](https://pypi.org/project/snap4n6)
