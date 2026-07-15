# Y9 2019 Revival: Stock Android Installation Guide

This phone was given to me, and because I can't stand EMUI, this project is dedicated to putting stock Android on it. 

**The Facts:**
Installing a Generic System Image (GSI) is the only way to get stock Android on the Huawei Y9 2019, as there are no custom ROMs built specifically for this device. 

I tested Android 11, 12L, and 14 GSIs. All of them experienced heavy SystemUI lag, making them unusable for daily driving. This led me to Android 10 (Havoc OS)—which is only one step above the last official EMUI 9, but the performance and experience are miles ahead.

> **Disclaimer:** Opening your phone and modifying system partitions carries risks. Proceed at your own risk. I am not responsible for bricked devices.

## Prerequisites
Before starting, ensure you have the following ready:
* A pair of tweezers (for the test point).
* A MicroSD card (highly recommended to keep the GSI and flashing files separate).
* Download the necessary repository files (`Code` -> `Download ZIP`).
* Android Utility Pro (AUP) installed on your PC.
* Minimal ADB and Fastboot (or Android Studio Platform Tools) installed.

---

## Phase 1: Unlocking the Bootloader
Unlocking the bootloader requires opening the phone and interacting with the motherboard's test points.

1. Open the phone and locate the test point. **Disconnect the battery first**, as having the battery connected at the start of this step can cause issues.
2. Use tweezers to ground both test points, then plug the phone into your PC.
3. Open Android Utility Pro (AUP) as Administrator, navigate to the **Huawei** tab, and select **Service**.
4. In the HiSilicon section, choose **Kirin 710** from the dropdown menu.
5. Click **Load Factory Fastboot**.
6. The software will prompt you to redo the test point. Disconnect the phone, ground the points again with the tweezers, and reconnect to the PC. The process will resume.
7. Once it says "Waiting for device (fastboot)", reconnect the phone's battery. It will connect to the PC successfully. *(If it throws an error, that is fine; we will use ADB commands in the next steps).*
8. Open Command Prompt on your PC and navigate to your ADB folder.
   * *Platform Tools path:* `C:\Users\User\AppData\Local\Android\Sdk\platform-tools`
   * *Minimal ADB path:* `C:\Program Files (x86)\Minimal ADB and Fastboot`
9. Type `fastboot devices` to verify your device is detected. 
10. Once detected, type `fastboot oem unlock`. On your phone screen, select the option to **Unlock this device and wipe**.
11. After the device wipes and reboots into EMUI, complete the initial setup. Go to **Settings > About Device** and tap **Build Number** multiple times to enable Developer Options.
12. Go to **Developer Options** and verify **OEM Unlock** is enabled. If it is greyed out, reboot the phone and connect to Wi-Fi a few times until it unlocks.
13. Power off the phone. Hold the **Power** and **Volume Down** buttons to boot into Fastboot. You should see "Bootloader Unlocked" and "FRP Unlock" on the screen, indicating success.

---

## Phase 2: Installing Custom Recovery (SHRP)
1. On your PC, move the `shrp_recovery.img` file into your ADB / Platform Tools folder.
2. Open Command Prompt, navigate to the ADB folder, and connect your phone to the PC while it is in Fastboot mode.
3. Type the following command:
   `fastboot flash recovery_ramdisk shrp_recovery.img`
4. Once the flash is complete, disconnect the phone.
5. Hold the **Power** and **Volume Up** buttons to boot directly into your new custom recovery.

---

## Phase 3: Flashing the GSI, GApps, and Root
*Note: I highly recommend using a MicroSD card for this phase to easily access your GSI, GApps, and Magisk files.*

1. Once SHRP is open, navigate to the Terminal and type: `twrp wipe system`
2. Go back to the main menu and select **Flash**. Click the three bars at the bottom and select your MicroSD card.
3. Locate the Havoc OS `.img` file, choose **System Image**, and swipe to flash. Wait for it to finish.
4. Flash your GApps `.zip` file to install Google services.
5. *(Optional)* For root access, flash the Magisk patched `.img` file and choose **erecovery_ramdisk**.
6. Go back to the main menu, select **Wipe**, then **Format Data**. Type `yes` and confirm.
7. Go back to the main menu and select **Reboot > System**. 
8. **Crucial Root Step:** When the "Your device has been unlocked and can't be trusted" warning appears during boot, always hold the **Volume Up** button to activate root. *(This is normal behavior; I have not yet checked if the boot image can be patched to bypass this requirement).*

If done correctly, your phone will boot into Havoc OS with the Play Store and Magisk installed.

---

## Additional Notes
For other GSIs that are larger than 3.5GB, please refer to the `resize.md` file in this repository for specific partition resizing instructions.
