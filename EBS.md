<h1>Elastic Block Store ( EBS )</h1>

<h1>Part 1: Attaching a New EBS Volume to an EC2 Instance</h1>

<h2>1. launch instance for a demo.<h2></h2>

   Please create a volume in the same Availability Zone as the EC2 instance.

    >>note  : We can attach the EBS volume of one EC2 instance to another EC2 instance—even if it's the root volume.

<h2>2. Create & Attach the Volume (via AWS Console)</h2>

 i. Open the EC2 Dashboard in the AWS Management Console.

 ii. Navigate to “Volumes” under Elastic Block Store.

 iii. Click “Create volume”:

   • Choose the volume type (e.g., GP2, GP3).

   • Set the size (in GiB).

   • Select the same Availability Zone as your EC2 instance.

iv. After creation, select the volume, click Actions → Attach volume.

v. Choose your target instance and specify a device name (e.g., /dev/xvdf).<br>
AWS requires instance and volume to be in the same AZ

<h2>3. Connect to the Instance & Format/Mount</h2>

     Once attached:

       ssh -i your-key.pem ec2-user@your-instance
   Then inside the instance:

       sudo lsblk   # display which devices are attached, and specify how many hard disks have been installed.
       df -h        # display the currently mounted devices, and indicate how much of the hard disk has been used.
       sudo file -s /dev/xvda1   # file system check command
Volume names are assigned under /dev because it is the primary directory in Linux designated for device files.as this directory is the designated area within the root filesystem for all device files.<br>
If you’d like to explore the /dev folder more

      cd /
      ls
 look dev directory
     
      cd dev
      ls

Now, let's examine the filesystem present on the newly attached disk

                        #here new created file name
     sudo file -s /dev/xvdaf
If you see the output data, then assume that there is no data in that file system. or not avalinle file system..
So, for that, the file system needs to be installed

              #make file system  #-t =types 
        sudo mkfs -t  xfs /dev/xvdbf
                            # kute install karyachay


  So, the file system is installed.<br>
  Now, check the file system.

         sudo file -s /dev/xvdaf
Now that the file system is installed, let’s mount it.<br>
To mount, you need to create a folder.

    ~ ]$   mkdir mydata
    ~ ]$   sudo mount /dev/xvdbf /homw/ec2-user/mydata/
           df -h

• Now that the HDD’s filesystem has been set up, you must mount it onto a directory (mount point). Whatever you do in that directory—like creating folders, reading, and writing—will happen on the HDD.<br>

• And If you intend to detach the file system ( volume ) , please unmount it first. Otherwise, there is a risk of data corruption .
>>Once detached, the volume can be attached to another EC2 instance—provided that both the volume and the instance reside in the same Availability Zone. After attaching, no further modifications are necessary; you simply create the folder and mount the volume, and it is ready for use.

>>To detach the volume: first, navigate to the EC2 console. Then, locate and select the desired volume under Elastic Block Store in the navigation pane. Finally, open the Actions menu and choose Detach Volume

unmount command :

        sudo umount /dev/xvdbf
        df -h
        cd mydata/
        ls

• Now that we have unmounted the filesystem, there is nothing left within the ‘mydata’ directory.

• What Actually Happens When You Unmount a Filesystem

<h3>Mounting Over a Directory</h3>

When you mount a filesystem (like an external HDD partition) onto a directory (e.g., /mydata), the directory's original contents become hidden, not deleted. These hidden files remain on disk and only reappear when the filesystem is unmounted 

<h3>A helpful explanation from Unix Stack Exchange clarifies:</h3>

>>The mount will hide any existing files in the directory used as a mount point. Nothing happens to the files themselves.

<h3>After Unmounting : </h3>

Once you unmount the filesystem, the hidden original files reappear, since you are now viewing the underlying directory, not the mounted filesystem<br>

   • The mount we have made is temporary—it remains in effect only while the server is powered on.<br>
   
   • If you require a permanent mount, open a console and execute the following command:<br>
        
        sudo nano /etc/fstab

   Then insert an entry such as:<br>

       /dev/xvdbf /foldername xfs defaults,nofail 0 2
   This will ensure the volume is mounted permanently.


<h1>Part 2: Extending the Size of an Existing EBS Volume</h1>

<h2>(A) Modify Volume Size Without Detaching (Elastic Volumes)</h2>

  1. In the console, go to Volumes, select the volume, then Actions → Modify Volume.

  2. Increase the size (and optionally performance or type) and click Modify 

  3. Confirm the modification has entered the optimizing or completed state 


<h2>(B) On the EC2 Instance: Expand Partition & File System</h2>

Once modification is done:

1. SSH into the instance:

       lsblk         # See updated volume size vs partition size
       df -h
2. If your disk uses partitions—and the partition is smaller—expand it:

         sudo growpart /dev/xvda 1  # For Xen
   Resize the file system based on its type:

   XFS:
   
         sudo xfs_growpfs /dev/xvda1
   Ext4:

       sudo resize2fs /dev/xvda1
   Verify the changes:
   
         df -h
              

 
 
   
  
