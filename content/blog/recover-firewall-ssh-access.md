---
title: "Recover from firewall not allowing ssh on port 22 in GCP instance"
date: 2019-04-26T08:30:00+05:30
author: Prashant Shahi
show_reading_time: true
toc: false
tags:
  - aws
  - firewall
  - gcp
  - ufw
---

![SSH Firewall](/images/ssh-firewall.png)

Tried to setup [UFW](https://help.ubuntu.com/community/UFW) or any other Firewall,
but it disallows any traffic to port 22, which doesn't let you ssh to your Google
Cloud Platform (GCP) or Aamazon Web Services (AWS) instance? 😖

This article might be your savior. 😎

Follow the instructions below :

1. Go to your VM's configuration page. Select the project. Then, Go to Compute ->
Compute Engine -> VM Instances. Click the VM you want to recover
2. Click Edit. Then, scroll down to Custom Metadata
3. Create a new metadata field. Set the key to startup-script. Set value to sudo ufw allow 22
4. Restart your VM
5. Now, ssh to the instance. Now, you should be able to get access to the VM
6. Heave a sigh of relief 😌

For [AWS EC2 Instance](https://aws.amazon.com/), the method is quite similar. But instead of
`startup-script` , we need to specify the script in the _User Data_ field. Read more about it here.

---

Originally posted in https://medium.com/@coolboi567/recover-from-firewall-not-allowing-ssh-on-port-22-in-gcp-instance-1379fc84522e
