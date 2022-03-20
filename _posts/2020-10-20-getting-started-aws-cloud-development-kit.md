---
layout: post
title: "Getting Started - AWS Cloud Development Kit"
author: "John Lukach"
tags: AWS CDK Cloud9 Python
---

##### GOODBYE LOCAL DEVELOPMENT

Two-point-five years into my nomadic lifestyle of using cellular data, I decided it was time to figure out my development environment. My primary goal was to lower my local bandwidth consumption while having a decent interface that I could use from macOS, iPadOS, and Linux operating systems. I code for fun mainly with Python on AWS infrastructure, making Cloud9 and CDK an excellent starting point.

![1-createenvironment](/images/2020/10/1-createenvironment.png)

Start by launching a Cloud9 environment with an identifiable name and optional description in the region with your current workloads.

![2-nameenvironment](/images/2020/10/2-nameenvironment.png)

I like to create a no-ingress EC2 instance for simplified environment access via System Manager since my public egress IP continually changes. Depending on your requirements, this may introduce some additional security considerations for your development bastion host.

https://docs.aws.amazon.com/systems-manager/latest/userguide/security-best-practices.html

![3-configuresettings](/images/2020/10/3-configuresettings.png)

I pick Amazon Linux 2 as my operating system of choice.  This helps with a serverless strategy making the addition of external Python libraries easier for Lambda development.

![4-platformsettings](/images/2020/10/4-platformsettings.png)

Now you can just open a terminal window in a specific lambda folder to quickly add compatible libraries.

```
python3 -m pip install --target=./ requests
```

Cloud9 deployments can be secured with VPC Endpoints for EC2 and SSM or, better yet, add a public/private subnet with NAT Gateway for your egress in production environments too.

![5-launchenvironment](/images/2020/10/5-launchenvironment.png)

##### PRETTY WEB UI

![6-AWSCloud9](/images/2020/10/6-AWSCloud9.png)

Ok, I can not look at the default theme for long, so I change that right out of the gate.

![7-Cloud9Preferences](/images/2020/10/7-Cloud9Preferences.png)

##### EXPAND VOLUME SIZE

The default installation only has 10 GB of storage allocated, not leaving enough room for all the necessary dependencies.  I would recommend a minimum of 20 GB for your disk storage starting point.

![8-modifyvolume](/images/2020/10/8-modifyvolume.png)

During the initial setup, the EC2 volume can fill up, requiring the temporary directory to be emptied to free up space.

```
sudo rm -R /tmp/*
```

These commands will finish expanding the disk space available for the operating system usage.

```
$ lsblk
NAME    MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
xvda    202:0    0  20G  0 disk 
└─xvda1 202:1    0  10G  0 part /
$ sudo growpart /dev/xvda 1
$ sudo reboot now
```

##### UPDATE PYTHON

Amazon Linux is typically behind on the current version of Python3 installed; thus, use Homebrew to update the installation with these commands.

```
$ sudo yum update -y
$ /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
$ sudo yum groupinstall 'Development Tools'
$ echo 'eval $(/home/linuxbrew/.linuxbrew/bin/brew shellenv)' >> /home/ec2-user/.bash_profile
$ eval $(/home/linuxbrew/.linuxbrew/bin/brew shellenv)
$ brew install gcc
$ brew install python@3.8
$ sudo reboot now
```

##### UPDATE CDK

AWS Cloud Development Kit (CDK) is continuously being improved!  I want to stay on the current version, where I regularly use these commands when I wake up the Cloud9 system or stand up new Python environments.

https://docs.aws.amazon.com/cloud9/latest/user-guide/sample-cdk.html

```
$ cdk version
1.66.0 (build 459488d)
**************************************************
*** Newer version of CDK is available [1.67.0] ***
*** Upgrade recommended                        ***
**************************************************
$ curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.0/install.sh | bash
$ nvm install stable
$ npm install -g typescript
$ npm install -g aws-cdk
```

##### BOOTSTRAP CDK

Here are the commands to use the modern bootstrap for your CDK deployments from your Cloud9 instance.

https://docs.aws.amazon.com/cdk/latest/guide/bootstrapping.html

```
$ export CDK_NEW_BOOTSTRAP=1
$ cdk bootstrap aws://ACCOUNT-NUMBER-1/REGION-1 --cloudformation-execution-policies arn:aws:iam::aws:policy/AdministratorAccess --trust DEPLOYMENT-ACCOUNT
```

##### GITHUB SSH KEYS

At the initial standup, AWS recommends using a source code repository to backup your Cloud9 environment. I choose to use GitHub, where I need to tie SSH Keys to my EC2 instance for commits.

```
$ ssh-keygen -t rsa
$ cat ~/.ssh/id_rsa.pub
$ eval $(ssh-agent -s)
$ ssh-add ~/.ssh/id_rsa
```

##### CREATE CDK PROJECT

The CDK Workshop provides a detailed tutorial for getting started! Like the rest of this post, I wanted to provide my quick start notes to create an empty CDK project.

https://cdkworkshop.com/30-python.html

```
$ mkdir new-app-cdk && cd new-app-cdk
$ cdk init app --language python
$ python3 -m venv .venv
$ source .venv/bin/activate
$ pip3 install -r requirements.txt --upgrade
```
