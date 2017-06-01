---
layout: post
title:  "Resizing a Windows 10 Virtual Disk Image From MacOS"
date:   2017-06-01 12:00:00:00
author: shaun
categories: windows10 virtualbox
---

I have a Windows 10 VM on my Mac, via VirtualBox. The virtual hard drive ran out of space. Looking in the UI, there doesn't seem to be a way to manually resize it:

![No resizing for you](/images/win10-vm-no-resize.png)

So how do you resize it?

# Step 1 - Resize the `.vdi` file

Go to the VirtualBox package.
```
$ cd /Applications/VirtualBox.app/Contents/Resources/VirtualBoxVM.app/Contents/MacOS
```

Run the command to resize the .vdi file. The new size must be passed in MB, in this example 60000 = 60GB.
```
$ VBoxManage modifyhd --resize 60000 ~/VirtualBox\ VMs/Windows\ 10/Windows\ 10.vdi
```

Confirm it was resized.
```
$ VBoxManage showhdinfo ~/VirtualBox\ VMs/Windows\ 10/Windows\ 10.vdi
<other info omitted>
Storage format: VDI
Format variant: dynamic default
Capacity:       60000 MBytes
Size on disk:   31991 MBytes
Encryption:     disabled
```

# Step 2 - Resize the partition in Windows to use the new space

1. Open the *Computer Management* ~~app~~ program.
1. Open *Storage*, then *Disk Management*.
1. Right click on your drive (probably *C:*). Select *Extend Volume*.
1. Extend it into the new space you created in step 1.

I'm not sure why you can't do this through VirtualBox's GUI, but this solution works just fine.

If you have any issues with this, stop using Windows and the problem will go away automatically. 
