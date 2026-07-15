# Resizing Partitions for Larger GSIs
# Disclaimer, had a bit of help from AI, just saying.

If the GSI you want to flash is larger than the Huawei Y9 2019's default `system` partition (typically anything over **3.5 GB**), you will need to manually resize your partitions.

## Warning

This process will **completely erase all data** on your device.

Incorrectly modifying the partition table can permanently brick your device. **Never blindly copy the partition numbers shown in this guide.** Always verify the partition numbers and sector values on your own device before making any changes.

## How It Works

To increase the size of the `system` partition, you must:

1. Delete the `system` partition.
2. Delete the partition immediately after it (usually `userdata`).
3. Recreate `system` with a larger ending sector.
4. Recreate `userdata` using the remaining free space.

---

## Prerequisites

- SHRP (SkyHawk Recovery Project) installed and booted
- A PC with ADB installed
- An ARM64 `parted` binary (available on XDA Developers or GitHub)

---

# Step-by-Step Guide

## 1. Push `parted` to the Device

Download the ARM64 `parted` binary, then push it to SHRP.

```bash
adb push parted /sbin/
adb shell
chmod +x /sbin/parted
```

---

## 2. Unmount All Partitions

Before editing the partition table, every partition must be unmounted.

In SHRP:

**Mount** → Uncheck **every** partition.

---

## 3. Launch Parted

Start `parted` on the internal storage device.

```bash
/sbin/parted /dev/block/mmcblk0
```

Inside the `parted` prompt, switch to sector units and print the current partition table.

```text
unit s
print
```

Using sectors ensures there are no alignment gaps.

---

## 4. Delete the Existing Partitions

Find the partition numbers for:

- `system`
- `userdata`

For example:

| Partition | Number |
|-----------|--------|
| system | 66 |
| userdata | 67 |

Delete them:

```text
rm 66
rm 67
```

> **Example only.** Your partition numbers may be different.

---

## 5. Recreate the Partitions

Recreate `system` first.

Suppose:

- Original system starts at `<original_start_sector>`
- Original system ends at `<original_end_sector>`
- You want a larger system partition ending at `<new_system_end_sector>`

Create it with:

```text
mkpart system ext2 <original_start_sector> <new_system_end_sector>
```

> `parted` may ask for a filesystem type. Enter `ext2`. SHRP will format it correctly afterward.

Next, recreate `userdata` beginning **one sector after** the new system partition.

```text
mkpart userdata ext2 <new_system_end_sector + 1> <original_userdata_end_sector>
```

---

## 6. Restore Partition Names

Sometimes `parted` removes partition names.

Rename them:

```text
name 66 system
name 67 userdata
```

Replace the numbers with the ones shown on **your** device.

---

## 7. Verify the Layout

Print the partition table again.

```text
print
```

Confirm:

- No overlapping sectors
- Correct partition names
- Correct start/end sectors

Exit `parted`.

```text
quit
```

---

## 8. Format the New Partitions

In SHRP:

### Format `system`

1. **Wipe**
2. **Advanced Wipe**
3. Select **System**
4. **Repair or Change File System**
5. **Change File System**
6. Select **Ext4**

### Format `userdata`

Repeat the same process for **Data**.

Format it using:

- **F2FS** (recommended if your device originally used it), or
- **Ext4**

---

## 9. Reboot Recovery

Reboot back into SHRP once.

This allows recovery to reload the updated partition table and recognize the new partition sizes.

---

# Done

Your `system` partition is now larger, allowing you to flash GSIs that previously exceeded the stock partition size.

You can now flash your GSI using:

- SHRP
- Fastboot
```
