# ⚡ TWRP Flash Guide — Lava Play Ultra 5G

<div align="center">

![Device](https://img.shields.io/badge/Device-Lava%20Play%20Ultra%205G-red?style=for-the-badge&logo=android)
![Method](https://img.shields.io/badge/Method-Fastboot-blue?style=for-the-badge)
![Partition](https://img.shields.io/badge/Partition-vendor__boot-orange?style=for-the-badge)

</div>

> ⚠️ **Read the entire guide before starting. Skipping steps may brick your device.**

---

## 📋 Prerequisites

- USB Debugging enabled
- ADB & Fastboot installed on your PC → [Download Platform Tools](https://developer.android.com/studio/releases/platform-tools)
- Original `vbmeta.img` from your stock firmware
- Your custom `vendor_boot.img` (TWRP build)

---

## 🔓 Step 1 — Unlock Bootloader

> 💡 **If your bootloader is already unlocked, skip to Step 2.**

### Enable OEM Unlocking on device:
1. Go to **Settings → About Phone**
2. Tap **Build Number** 7 times until you see *"You are now a developer!"*
3. Go back to **Settings → System → Developer Options**
4. Enable **OEM Unlocking** ✅
5. Enable **USB Debugging** ✅

### Unlock via ADB & Fastboot:

**If your device already has ADB access:**
```bash
adb reboot bootloader
```

**Once in fastboot mode:**
```bash
fastboot flashing unlock
```

On your device screen, use **Volume Up/Down** to navigate and confirm:
- **Volume Up** = Yes, unlock bootloader ✅
- **Volume Down** = No, cancel ❌

> ⚠️ **Unlocking the bootloader will wipe all data on your device!**

**If you rebooted back to system after unlock:**
1. Complete the initial setup
2. Re-enable **Developer Options** and **USB Debugging**
3. Run `adb reboot bootloader` again to get back to fastboot

---

## 🛡️ Step 2 — Disable vbmeta Verification

This step disables Android Verified Boot checks so TWRP can boot correctly:

```bash
fastboot flash vbmeta --disable-verity --disable-verification vbmeta.img
```

> ✅ `vbmeta.img` is found inside your extracted stock firmware folder.

---

## 💾 Step 3 — Backup Your Stock vendor_boot

**Always back up before touching any partition!**

```bash
fastboot fetch vendor_boot vendor_boot_stock_backup.img
```

> ⚠️ If you get an error with the above command, use one of these alternatives:
> - **SP Flash Tool** → Use the **Readback** feature to pull the `vendor_boot` partition
> - **Download** the stock `vendor_boot.img` from your device firmware package

Keep this backup somewhere safe — it's your way back if anything goes wrong! 🔒

---

## 🚀 Step 4 — Flash TWRP to Inactive Slot

Since the **Lava Play Ultra 5G is an A/B device**, you can safely flash TWRP to the **inactive slot** — the slot you are NOT currently booted into. This means if something goes wrong, your current slot is untouched.

### Check your current active slot:
```bash
fastboot getvar current-slot
```

### Flash to the inactive slot:

**If you are currently on Slot A → flash to Slot B:**
```bash
fastboot flash vendor_boot_b vendor_boot.img
```

**If you are currently on Slot B → flash to Slot A:**
```bash
fastboot flash vendor_boot_a vendor_boot.img
```

---

## 🔄 Step 5 — Switch Slot & Boot into TWRP

Switch to the slot you just flashed and reboot into recovery:

```bash
fastboot --set-active=b
fastboot reboot recovery
```

> Replace `b` with `a` if you flashed to Slot A.

If everything went well, your device will boot into **TWRP recovery**! 🎉

---

## ↩️ How to Revert Back to Stock Recovery

If something goes wrong or you want to go back, flash your backup:

```bash
fastboot flash vendor_boot vendor_boot_stock_backup.img
fastboot --set-active=a
fastboot reboot
```

---

## ❓ Common Errors

| Error | Fix |
|-------|-----|
| `fastboot devices` shows nothing | Reinstall MTK drivers, try different USB port |
| `FAILED (remote: not allowed)` | OEM unlock not enabled, check Developer Options |
| `fastboot fetch` not working | Use SP Flash Tool Readback to backup vendor_boot |
| Device bootloops after flash | Revert using stock backup via SP Flash Tool |

---

## 💬 Need Help?

Join our Telegram support channel:

[![Telegram](https://img.shields.io/badge/Telegram-Support%20Channel-blue?style=for-the-badge&logo=telegram)](https://t.me/lava_play_ultra)

---

<div align="center">

Made with ❤️ by the kotler-m2, for the community.

</div>
