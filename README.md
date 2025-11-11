# Proxmox-HomeLab-Upgrade
A step by step guide to optimizing and configuring my proxmox home lab

# 🧠 My Proxmox Lab Upgrade Journey  

I’ve always wanted to take my home lab a little further, not just add more hardware, but truly give myself a space where I could **build freely**, break things, fix them, and push limits without worrying about running out of resources.  

That thought kept coming back to me each time I hit a technical ceiling. I’d be halfway through a test lab or simulation and think:  
> “If only I had a bit more compute or storage...”  

So one weekend, I decided to stop thinking and start building.  

---

## ⚙️ The Hardware  

I had two spare PCs lying around:  

- **Lenovo** — Core i5, 16 GB RAM, 525 GB SSD  
- **Acer** — Core i3, 8 GB RAM, 1 TB HDD  

I wanted to merge their strengths. The plan was simple; use the Lenovo for its stronger processor and add the 1 TB hard drive from the Acer.  

After some tinkering, I ended up with a compact but capable setup:  
> 💻 **5 cores, 16 GB RAM, and 1.5 TB total storage**  

Probably more than I need right now, but I’ve never been one to leave performance on the table.  

---

## 🚀 Getting Proxmox Ready  

Once I configured the BIOS and enabled virtualization, it was game on.  
Proxmox installed smoothly, but there was one snag, my newly added 1 TB drive wasn’t showing up after each reboot.  

I needed it to mount automatically every time the system started.  

---

## 🧩 The Fix  

### 1. Format the Disk  

    mkfs.ext4 /dev/sdb1


This formats the disk with an ext4 file system, preparing it for use.

### 2. Create a Partition Table

    parted /dev/sdb mklabel gpt
    parted /dev/sdb mkpart primary ext4 0% 100%
    lsblk

Running lsblk confirmed that my new partition was created successfully.


### 3. Make the Mount Persistent

First, I retrieved the UUID of the new drive:

    blkid /dev/sdb1

Then I opened the fstab configuration file:

    sudo nano /etc/fstab


Inside the editor, I added:

    UUID=<my-uuid> /mnt/Vault ext4 defaults 0 2

  This tells Linux to automatically mount the drive at boot time.


### 4. Test and Verify

To test without rebooting:

    sudo mount -a

I initially got an error, turned out my mount point used lowercase instead of uppercase.
After fixing the directory name, everything worked as expected.

Then I confirmed it:

    lsblk


And reloaded the system configuration:

    systemctl daemon-reload

---

## 🧠 Lessons Learned

This small upgrade turned into a hands-on lesson in system configuration, disk management, and persistence.

It reminded me that even the simplest setups teach something deeper; how each layer of a system works together, from BIOS virtualization to filesystem mounting.

Now my Proxmox environment boots seamlessly with both drives ready to go. I’ve got more room to experiment, spinning up VMs, running containers, or simulating entire environments.

And that’s what a lab is meant to be:

a place to build, break, learn, and rebuild endlessly.

--

🔧 Stack Overview
Component	Description
Host	Lenovo Core i5 (16 GB RAM, 525 GB SSD)
Additional Drive	1 TB HDD (mounted via fstab)
Virtualization Platform	Proxmox VE
File System	ext4
Storage Mount Point	/mnt/Vault



## 🧩 Next Steps

- **Add network automation (Ansible or Terraform integration)
- **Test multi-node configuration
- **Explore containerized service clusters



---

Documented as part of my ongoing lab build series; exploring system design, security, and infrastructure hands-on.
