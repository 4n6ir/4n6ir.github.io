---
layout: post
title: "Snapshot 4n6ir Imager Initial Release"
author: "John Lukach"
tags: AWS EBS Python
---

##### HISTORY

A long time ago, I used to have to pull hard drives from a computer and attach it to a write blocker for imaging. Then traditional forensic tools released agents that got deployed to all machines for remote imaging. Both of these options were not practical due to bandwidth and time consumption that limited the feasibility. Due to these limitations, forensic analysts had no choice but to make targeted acquisitions for specific operating system artifacts. It helped to triage cases quickly without always requiring a full disk image.  Sometimes there was no choice when the artifacts were overwritten or short-lived without other supporting logs or packets. Along came virtualization and cloud computing, making these systems requiring investigation very ephemeral, significantly limiting the imaging options available.

##### AMAZON WEB SERVICES (AWS)

Amazon Elastic Block Store (EBS) Snapshot is a backup solution that incrementally captures blocks on devices that have changed for an Elastic Cloud Compute (EC2) instance. The analyst can restore the snapshot to a new volume that could be attached to a Linux system as read-only for imaging. Amazon recommends stopping the EC2 instance before taking an EBS Snapshot of root volumes since EXT4 is a delayed write file system, and NTFS is a lazy write file system. This practice should not happen during your investigation as it would destroy volatile artifacts that could be key to your success!

In June of 2016, AWS released a feature to share Snapshots cross accounts as it is recommended not to conduct your examination in the account under investigation.  The EC2 instance needs an initial volume set up with AWS Key Management Service (KMS) using a Customer Master Key (CMK) to transfer the data safely. Snapshots of unencrypted EBS volumes can be shared but is an unacceptable risk. EBS volumes encrypted with Amazon managed KMS encryption are not sharable, forcing the investigation to happen in the impacted account.  

In May of 2019, AWS added the ability to take point-in-time, crash-consistent snapshots across multiple volumes for an EC2 instance. It significantly improved the file system syncing for analysis but did not change the recommendations around root volumes.

In November of 2019, AWS released the EBS Fast Snapshot Restore (FSR) feature that, for a cost, drastically sped up the process of restoring a snapshot to a new volume.  

Amazon Web Services (AWS) released direct API access to Elastic Block Storage (EBS) Snapshot content at AWS re:Invent 2019.  Providing an opportunity to resolve issues examiners have been facing for some time in acquiring an image. The Python Boto3 library, once properly authenticated with access to the AWS Key Management Service (KMS) and Amazon Elastic Block Store (EBS), could capture a decrypted image with the ListSnapshotBlocks and GetSnapshotBlock actions.

##### SNAPSHOT 4N6IR IMAGER

Snapshot 4n6ir Imager python script initial release for converting an Amazon EBS Snapshot to a DD image for analysis. Special thanks to those who followed along with my previous posts by testing and providing feedback! Hopefully, others find my weekend project helpful in addressing the AWS imaging hurdles.

Happy Coding,

John Lukach

```
Snapshot 4n6ir Imager v0.1.7

optional arguments:
  -h, --help           show this help message and exit

Required:
  --region REGION      us-east-2
  --snapshot SNAPSHOT  snap-056e0b1bd07ad91b2

Voluntary:
  --budget             API Quantity & Download Size
  --compress           Compress EBS Snapshot Blocks
  --decrypt            Decrypt Compressed EBS Snapshot Blocks
  --encrypt            Encrypt Compressed EBS Snapshot Blocks
  --ext4               Rebuild EXT4 File System
  --image              Image EBS Snapshot Blocks
  --ntfs               Rebuild NTFS File System
  --password PASSWORD  Encryption & Decryption Password
  --salt SALT          Encryption & Decryption Salt
  --uncompress         Uncompress EBS Snapshot Blocks
  --verify             Verify EBS Snapshot Blocks
```

##### INSTALLATION

Choose your favorite flavor of Linux, use your daily Mac OS X driver or even pick the Windows Subsystem for Linux with Python 3 installed. Add two Python libraries, download the script, and you are all set to go after validating the SHA256 hash of the file. The script runs with a single core and 512 MB of memory, if necessary.  Adding processor improves performance for compression, encryption, and hash validation. Adding drive IOPs speeds up the image rebuild for EXT4 and NTFS file systems. If the resources are available, I would add both to your analysis machine to save time, allowing the investigation to start sooner.

Installation for Ubuntu 18.04

```
$ sudo apt-get install python3-pip -y
$ pip3 install boto3 tqdm
```

##### BUDGET

The number of API calls, data transfer costs, and restoration EBS volume size information are required to provide a cost estimate. Amazon’s Boto3 documentation for the direct API access to Elastic Block Storage (EBS) Snapshot does not provide the maximum results upper limit (10k) for the list_snapshot_blocks() action. The API Quantity below is the number of get_snapshot_block() calls required that are divisible by 10k to determine the costs of both APIs. Download Size would be the uncompressed volume to be pulled to determine transfer costs. Volume Size is the amount of EBS disk storage required to rebuild the image.

```
$ python3 Snapshot-4n6ir-Imager.py --region us-east-2 --snapshot snap-000bccfd69051b583 --budget
Snapshot 4n6ir Imager v0.1.7

Region: 	us-east-2
Snapshot: 	snap-000bccfd69051b583

100%|████████████████████████████████████████████████████| 2730/2730 [00:00<00:00, 964248.41it/s]

API Quantity: 	2730
Download Size: 	1.33 GB
Volume Size: 	8 GB
```

##### IMAGING

Each Snapshot block (512K) writes to a folder named after the Snapshot identification number as an individual file. The file name includes the following information:

- block index number
- snapshot id
- sha256 of block content for verification
- total volume size
- block size

```
$ python3 Snapshot-4n6ir-Imager.py --region us-east-2 --snapshot snap-000bccfd69051b583 --image
```

##### VERIFICATION

The integrity of the collected evidence is essential to have confidence in the results of the analysis.

```
$ python3 Snapshot-4n6ir-Imager.py --region us-east-2 --snapshot snap-000bccfd69051b583 --verify
```

##### REBUILD IMAGE

Empty DD image gets created for both EXT and NTFS file systems as part of rebuilding the images. Linux has an extra step where the superblock gets overlaid onto the image file. The block index and block size calculate the offset to restore the data to the correct disk location.
```
$ python3 Snapshot-4n6ir-Imager.py --region us-east-2 --snapshot snap-000bccfd69051b583 --ext4

   or
   
$ python3 Snapshot-4n6ir-Imager.py --region us-east-2 --snapshot snap-000bccfd69051b583 --ntfs
```

##### COMPRESSION

Data transfer and storage costs are the most expensive part of the imaging process of an EC2 instance volume that the GZIP compression reduces. Compression should always occur before encryption to get the most benefit.

```
$ python3 Snapshot-4n6ir-Imager.py --region us-east-2 --snapshot snap-000bccfd69051b583 --compress

   or
   
$ python3 Snapshot-4n6ir-Imager.py --region us-east-2 --snapshot snap-000bccfd69051b583 --uncompress
```

##### ENCRYPTION

Required for secure transfer unless the EBS volume was initially set up with AWS Key Management Service (KMS) using a Customer Master Key (CMK) for encryption.

```
$ python3 Snapshot-4n6ir-Imager.py --region us-east-2 --snapshot snap-000bccfd69051b583 --encrypt --password password --salt salt

   or
   
$ python3 Snapshot-4n6ir-Imager.py --region us-east-2 --snapshot snap-000bccfd69051b583 --decrypt --password password --salt salt
```

##### ANALYSIS

![Snapshot-4n6ir-Imager](/images/2020/03/snapshot-4n6ir-imager.png)
