# Introduction
Here is some recommendation that you should adapte to your Ubuntu VM if your running it in Hyper-V so you will get out the most of the machine.

## VHD or VHDX?
Well, today you should choose VHDX over VHD, VHDX is much better and we should use it.
If you want to read more about why https://blogs.technet.microsoft.com/ausoemteam/2015/04/24/deciding-on-when-to-use-vhdx-or-vhd-files-with-hyper-v/

#### Dynamic or fixed
You should always choose fixed, it will give you the best performance.
Don't you worry, if you want to expand the VHDX later you can do so, read NohaTech-Ubuntu-expand-VHDX-English.md.
https://github.com/NohaTech/Seafile-best-practices-for-Ubuntu/blob/master/NohaTech-Ubuntu-expand-VHDX-English.md

Also if you want to replace the harddrive that the VHDX are placed on with a bigger or faster one or whatever you can also do that just shutdown the Hyper-V VM and then move the VHDX or VHD to the new harddrive, then in the VM's settings choose the new location of the VHDX or VHD.

## RAM
You should always multiply the RAM with 128. For example 128*9= 1152MB this is a recommendation from Microsoft. And if your not doing it, it can cause issues.

## Install add-ons.
As we are running a VM we need to install some add-ons so it can communicate with Hyper-V so you can for a example do a live backup with Windows Server Backup.

First we need to install the Virtual kernel.
```
 sudo apt-get install linux-virtual-lts-xenial
```
Then we need to install the add-ons that are needed.
```
 sudo apt-get install linux-tools-virtual-lts-xenial linux-cloud-tools-virtual-lts-xenial
```

## Backup
You can use Windows Server Backup to backup your VM's and it works greate I have done it for a long time and it works greate with a external or internal harddrive.
I can personly recommend you to run it atleast one time every 24h, it's easy to setup with a schedual in Windows Server Backup.

The first time your going to do the backup it will take some time, but after that it only backups what you have changed.
And it will only backup the used space of the VHDX so if you have created a VHDX for 2TB but you have only used 1TB the backup size will only be 1TB.
I personly are running the backup's every night with start at 2AM (02:00) as I know that it's not many people that are using my Seafile server then.
But it's no problem if they are using it, it can still do a backup of it and it's a live backup so the server will not go down.

The only limitation is that if you want to back it up to a network drive you can only have one backup, so if you want history of backups you should use external or a internal harddrive.

## End notes
Well this was not much, but it's important so you can have a working VM without any issues and also so you can get out the most of it.
