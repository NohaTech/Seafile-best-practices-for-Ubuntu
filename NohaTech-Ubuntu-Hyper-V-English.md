# Introduction
Here is some recommendation that you should adapte to your Ubuntu VM if your running it in Hyper-V so you will get out the most of the machine.

## VHD or VHDX?
Well, today you should choose VHDX over VHD, VHDX is much better and we should use it.
If you want to read more about why https://blogs.technet.microsoft.com/ausoemteam/2015/04/24/deciding-on-when-to-use-vhdx-or-vhd-files-with-hyper-v/

## RAM
First off, you should always multiply the RAM with 128. For example 128*9= 1152MB this is a recommendation from Microsoft.

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

## End notes
Well this was not much, but it's important so you can have a working VM without any issues and also so you can get out the most of it.
