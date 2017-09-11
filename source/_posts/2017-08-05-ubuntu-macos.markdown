---
layout: post
title:  "add the shared folder for virtual ubuntu 16.04 LTS"
date:   2017-08-05 21:00:00 +1000
categories: ubuntu LTS
---

## env

host: mac os
virtual box
virtual linux: ubuntu 16.04 LTS

share the folder to the virtual machine, ubuntu 16.04 LTS

## steps

on virtual box, click 'settings'->'shared folder'
MUST select '/Users/Shared' as the shared folder on mac host


![vm folder screenshot](/images/vm_shared_folder.png)


on ubuntu 16.04 LTS, must install the virtual box driver


```

sudo apt-get install virtualbox-guest-dkms
```

```

sudo mount -t vboxsf -o uid=$UID,gid=$(id -g) Shared /mnt/
```

the shared folder is mounted '/mnt' folder
