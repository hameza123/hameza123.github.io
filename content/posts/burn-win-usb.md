+++ 
draft = true
date = 2025-10-03T19:07:13+01:00
title = "Creating Bootable Windows 11 USB on macOS"
description = "Complete Step-by-Step Guide: Creating Bootable Windows 11 USB on macOS "
slug = "Complete Step-by-Step Guide: Creating Bootable Windows 11 USB on macOS "
authors = "Seclice"
tags = ["win11", "macos", "usb", "boot", ""]
categories = ["Guide"]
series = []
+++


# Complete Step-by-Step Guide: Creating Bootable Windows 11 USB on macOS

## Prerequisites
- Windows 11 ISO file
- USB drive (16GB or larger recommended)
- macOS computer
- Administrator access

---

## Step 1: Download Windows 11 ISO
1. Go to [Microsoft Windows 11 Download Page](https://www.microsoft.com/software-download/windows11)
2. Download the Windows 11 ISO file
3. Save it to your Downloads folder

## Step 2: Mount the ISO File
```bash
# Double-click the ISO file in Finder, or use this command:
hdiutil mount ~/Downloads/Win11_23H2_English_x64.iso
```
- Note the mounted volume name (usually something like "CCCOMA_X64FRE_EN-US_DV9")
- Let's call this `SOURCE_VOLUME`

## Step 3: Identify Your USB Drive
```bash
diskutil list
```

**Example Output:**
```
/dev/disk0 (internal, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      GUID_partition_scheme                        *500.1 GB   disk0
   
/dev/disk2 (external, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:     FDisk_partition_scheme                        *15.5 GB    disk2
   1:                 DOS_FAT_32 UNTITLED                15.5 GB    disk2s1
```

**Identify your USB drive:**
- Look for the external physical disk with your USB's size
- **Important:** Note the disk identifier (e.g., `/dev/disk2`)
- **Warning:** Using the wrong disk will erase your data!

## Step 4: Unmount the USB Drive
```bash
# Replace disk2 with your actual USB disk identifier
diskutil unmountDisk /dev/disk2
```

## Step 5: Format the USB Drive

### Option A: exFAT (Recommended - No File Size Limits)
```bash
diskutil eraseDisk ExFAT "WIN11" MBR /dev/disk2
```

### Option B: FAT32 (Older BIOS Compatibility)
```bash
diskutil eraseDisk MS-DOS "WIN11" MBR /dev/disk2
```

**Wait for the format to complete** - you'll see a message like:
```
Finished erase on disk2
```

## Step 6: Copy Windows Files

### If using exFAT (Option A - Simple Copy):
```bash
# Copy all files from ISO to USB
rsync -avh --progress "/Volumes/CCCOMA_X64FRE_EN-US_DV9/" "/Volumes/WIN11/"
```

### If using FAT32 (Option B - Split Large Files):
```bash
# First, copy all files except the large install.wim
rsync -avh --progress --exclude="sources/install.wim" "/Volumes/CCCOMA_X64FRE_EN-US_DV9/" "/Volumes/WIN11/"

# Install wimlib for file splitting
brew install wimlib

# Split the large install.wim file into smaller parts
wimlib-imagex split "/Volumes/CCCOMA_X64FRE_EN-US_DV9/sources/install.wim" "/Volumes/WIN11/sources/install.swm" 3800
```

## Step 7: Make USB Bootable

### For UEFI Systems (Most Modern Computers):
```bash
# Install bootmgfw.efi to make USB UEFI bootable
sudo cp "/Volumes/CCCOMA_X64FRE_EN-US_DV9/efi/boot/bootx64.efi" "/Volumes/WIN11/efi/boot/bootx64.efi"
```

### For Legacy BIOS Systems:
```bash
# Make the partition active
sudo fdisk -e /dev/disk2
# At the fdisk prompt, type:
print          # View partitions
flag 1         # Set partition 1 as active
write          # Write changes
quit           # Exit
```

**But you can try without this step, I didn't do it and it works just fine for me**

## Step 8: Verify the USB Contents
```bash
# Check the USB structure
ls -la "/Volumes/WIN11/"
```

**You should see these key folders/files:**
```
/efi/
/sources/
/boot/
/bootmgr
/setup.exe
```

## Step 9: Eject the USB Safely
```bash
# Eject the Windows ISO
hdiutil unmount "/Volumes/CCCOMA_X64FRE_EN-US_DV9"

# Eject the USB drive
diskutil eject /dev/disk2
```

## Step 10: Test the Bootable USB

1. **Insert USB into target Windows computer**
2. **Restart the computer**
3. **Enter BIOS/UEFI settings** (usually F2, F12, Del, or Esc during boot)
4. **Change boot order** to boot from USB first
5. **Save and exit** - Windows setup should start

---

## Troubleshooting

### Common Issues:

**1. "No bootable device" error:**
- Try recreating with FAT32 instead of exFAT
- Ensure MBR partition scheme is used
- Check if target computer supports UEFI/Legacy boot

**2. File too large for FAT32:**
- Use exFAT format instead
- Or ensure wimlib split the install.wim properly

**3. USB not showing in boot menu:**
- Try different USB port (USB 2.0 often works better)
- Recreate with `GPT` instead of `MBR` for UEFI systems:
  ```bash
  diskutil eraseDisk ExFAT "WIN11" GPT /dev/disk2
  ```

**4. "Windows cannot be installed to this disk":**
- The USB might not be properly bootable
- Recreate using the exact steps above

---

## Complete Script Version

Save this as `create-win11-usb.sh`:

```bash
#!/bin/bash

echo "=== Windows 11 USB Creator for macOS ==="
echo

# Get source volume
read -p "Enter Windows ISO mounted volume path: " SOURCE_VOLUME
read -p "Enter USB disk identifier (e.g., disk2): " USB_DISK

echo
echo "WARNING: This will erase ALL data on /dev/$USB_DISK!"
read -p "Continue? (y/N): " CONFIRM

if [[ $CONFIRM != "y" && $CONFIRM != "Y" ]]; then
    echo "Operation cancelled."
    exit 1
fi

echo
echo "Step 1: Unmounting USB drive..."
diskutil unmountDisk /dev/$USB_DISK

echo "Step 2: Formatting as exFAT..."
diskutil eraseDisk ExFAT "WIN11" MBR /dev/$USB_DISK

echo "Step 3: Waiting for drive to mount..."
sleep 5

echo "Step 4: Copying Windows files..."
rsync -avh --progress "$SOURCE_VOLUME/" "/Volumes/WIN11/"

echo "Step 5: Making USB bootable..."
sudo cp "$SOURCE_VOLUME/efi/boot/bootx64.efi" "/Volumes/WIN11/efi/boot/bootx64.efi"

echo "Step 6: Ejecting drives..."
hdiutil unmount "$SOURCE_VOLUME"
diskutil eject /dev/$USB_DISK

echo
echo "=== SUCCESS: Windows 11 USB created! ==="
echo "USB is ready for use. Insert into target computer and boot from USB."
```

Make it executable and run:
```bash
chmod +x create-win11-usb.sh
./create-win11-usb.sh
```

This detailed guide should help you create a bootable Windows 11 USB drive successfully from macOS.

