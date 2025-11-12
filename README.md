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



<p align="center">
  <img src="https://raw.githubusercontent.com/JoshWills69/Proxmox-HomeLab-Upgrade/7da8aaa4851e182a9acbc2ebfa1590c2752a6d96/ssd-hdd.jpg" width="45%" />
  <img src="https://raw.githubusercontent.com/JoshWills69/Proxmox-HomeLab-Upgrade/7da8aaa4851e182a9acbc2ebfa1590c2752a6d96/ssh-hdd1.jpg" width="45%" />
</p>



I wanted to merge their strengths. The plan was simple; use the Lenovo for its stronger processor and add the 1 TB hard drive from the Acer.  

After some tinkering, I ended up with a compact but capable setup:  
> 💻 **5 cores, 16 GB RAM, and 1.5 TB total storage**  

<p align="center">
  <img src="https://raw.githubusercontent.com/JoshWills69/Proxmox-HomeLab-Upgrade/7da8aaa4851e182a9acbc2ebfa1590c2752a6d96/server.jpg" alt="Proxmox Disk Layout" width="500">
</p>

Probably more than I need right now, but I’ve never been one to leave performance on the table.  

---

## 🚀 Getting Proxmox Ready  

<p align="center">
  <img src="https://raw.githubusercontent.com/JoshWills69/Proxmox-HomeLab-Upgrade/7da8aaa4851e182a9acbc2ebfa1590c2752a6d96/1image.png" alt="Proxmox Disk Layout" width="500">
</p>

Once I configured the BIOS and enabled virtualization, it was game on.  
Proxmox installed smoothly, but there was one snag, my newly added 1 TB drive wasn’t showing up after each reboot.  

<p align="center">
  <img src="https://raw.githubusercontent.com/JoshWills69/Proxmox-HomeLab-Upgrade/7da8aaa4851e182a9acbc2ebfa1590c2752a6d96/image3.png" alt="Proxmox Disk Layout" width="500">
</p>

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

<p align="center">
  <img src="https://raw.githubusercontent.com/JoshWills69/Proxmox-HomeLab-Upgrade/7da8aaa4851e182a9acbc2ebfa1590c2752a6d96/image4.png" width="45%" />
  <img src="https://raw.githubusercontent.com/JoshWills69/Proxmox-HomeLab-Upgrade/7da8aaa4851e182a9acbc2ebfa1590c2752a6d96/image5.png" width="45%" />
</p>


Running lsblk confirmed that my new partition was created successfully.

<p align="center">
  <img src="https://raw.githubusercontent.com/JoshWills69/Proxmox-HomeLab-Upgrade/7da8aaa4851e182a9acbc2ebfa1590c2752a6d96/image6.png" alt="Proxmox Disk Layout" width="500">
</p>


### 3. Make the Mount Persistent

First, I retrieved the UUID of the new drive:

    blkid /dev/sdb1

Then I opened the fstab configuration file:

    sudo nano /etc/fstab


<p align="center">
  <img src="https://raw.githubusercontent.com/JoshWills69/Proxmox-HomeLab-Upgrade/7da8aaa4851e182a9acbc2ebfa1590c2752a6d96/image8.png" width="45%" />
  <img src="https://raw.githubusercontent.com/JoshWills69/Proxmox-HomeLab-Upgrade/7da8aaa4851e182a9acbc2ebfa1590c2752a6d96/image9.png" width="45%" />
</p>

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

<p align="center">
  <img src="https://raw.githubusercontent.com/JoshWills69/Proxmox-HomeLab-Upgrade/7da8aaa4851e182a9acbc2ebfa1590c2752a6d96/image10.png" width="45%" />
  <img src="https://raw.githubusercontent.com/JoshWills69/Proxmox-HomeLab-Upgrade/7da8aaa4851e182a9acbc2ebfa1590c2752a6d96/image11.png" width="45%" />
</p>


---

Successfully mounted and recognized by the server after boot!!


<p align="center">
  <img src="https://raw.githubusercontent.com/JoshWills69/Proxmox-HomeLab-Upgrade/7da8aaa4851e182a9acbc2ebfa1590c2752a6d96/image12.png" alt="Proxmox Disk Layout" width="500">
</p>


# Before & After Overview

<p align="center">
  <img src="https://raw.githubusercontent.com/JoshWills69/Proxmox-HomeLab-Upgrade/7da8aaa4851e182a9acbc2ebfa1590c2752a6d96/image13.png" width="45%" />
  <img src="https://raw.githubusercontent.com/JoshWills69/Proxmox-HomeLab-Upgrade/7da8aaa4851e182a9acbc2ebfa1590c2752a6d96/image14.png" width="45%" />
</p>


## 🧠 Lessons Learned

- This small upgrade turned into a hands-on lesson in system configuration, disk management, and persistence.

- It reminded me that even the simplest setups teach something deeper; how each layer of a system works together, from BIOS virtualization to filesystem mounting.

- Now my Proxmox environment boots seamlessly with both drives ready to go. I’ve got more room to experiment, spinning up VMs, running containers, or simulating entire environments.

And that’s what a lab is meant to be:

a place to build, break, learn, and rebuild endlessly.

--

# 🔧 Stack Overview



| Component                   | Description                            |
| --------------------------- | -------------------------------------- |
| **Host**                    | Lenovo Core i5 (16 GB RAM, 525 GB SSD) |
| **Additional Drive**        | 1 TB HDD (mounted via fstab)           |
| **Virtualization Platform** | Proxmox VE                             |
| **File System**             | ext4                                   |
| **Storage Mount Point**     | `/mnt/Vault`                           |



## 🧩 Next Steps

- Add network automation (Ansible or Terraform integration)
- Test multi-node configuration
- Explore containerized service clusters



---

Documented as part of my ongoing lab build series; exploring system design, cloud security, and cloud infrastructure hands-on.
