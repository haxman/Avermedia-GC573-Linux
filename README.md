# AVerMedia Live Gamer 4K (GC573) - Linux Driver (Kernel 6.19+ Development)

This repository contains a community-maintained, AI-assisted and heavily patched version of the AVerMedia GC573 Linux driver. It has been modernized for the latest kernels, but **is currently in an unstable state.**

### ⚠️ Essential Kernel Parameter (Intel Users)
Since the proprietary binary blob is incompatible with Intel's Indirect Branch Tracking (IBT), you **must** disable it in your bootloader, or the module will fail to load.

**Add this to your kernel command line:**
`ibt=off`

## 🚀 Status: ⚠️ UNSTABLE / BROKEN / WIP

*   **Kernel Compatibility:** Builds and compiles on **Kernel 6.19.6** (CachyOS / Arch).
*   **Current State:** 
    *   **Module Loading:** Successfully loads (often auto-loads if previously installed).
    *   **Signal Detection:** Hardware successfully syncs (ITE6805 fallback). Kernel logs confirm **4K/60Hz sync**, but V4L2 reporting is stuck at "no power".
    *   **Capture:** **NOT FUNCTIONAL.** Attempting to capture or preview results in no image data or system instability.
    *   **Teardown:** **CRITICAL BUG.** Attempting to unload the module (`rmmod`) or using forced removal causes a **complete system freeze**.

## ✨ Recent Improvements (Under Investigation)

### 1. Modern Kernel Support (V4L2 & IBT)
- **API Modernization**: Updated to the current 2-argument `v4l2_fh_add/del` signatures.
- **Intel IBT (CET) Compatibility**: Integrated `MODULE_INFO(ibt, "N")` and custom CFLAGS to allow the proprietary binary blob to execute on modern CPUs.

### 2. Stability Architecture (Workqueues)
- **Tasklet to Workqueue Conversion**: Migrated the legacy tasklet-based DPC mechanism to a modern workqueue to prevent "scheduling while atomic" panics.
- **Context-Aware Locking**: Implemented logic in system wrappers to detect atomic contexts and avoid illegal sleeping.

### 3. I2C & Signal Logic
- **Linker Fallback**: Implemented manual `attach` for the ITE6805 chip to overcome vendor library limitations.
- **Device Registration**: Added manual registration logic to expose the capture chip's status to the V4L2 layer.

## 🛠️ How to Build & Install (FOR DEVELOPERS ONLY)

**WARNING: Testing this driver may lead to a hard lockup/system freeze.**

### Installation

1.  **Clone the repository:**
    ```bash
    git clone https://github.com/realEverlite/Avermedia-GC573-Linux.git
    cd Avermedia-GC573-Linux
    ```

2.  **Build:**
    ```bash
    ./build.sh LLVM=1
    ```

3.  **Load:**
    ```bash
    sudo insmod driver/cx511h.ko
    ```

## ⚠️ Known Blockers
1.  **System Freeze on Unload**: A deadlock or reference count mismanagement during teardown leads to a hard crash during `rmmod`.
2.  **Missing Video Stream**: Although signal sync is confirmed, the bridge between the proprietary blob and V4L2 DMA buffers is not yet delivering frames.

## 🤝 Seeking Contributors
I am looking for co-maintainers and contributors with experience in:
- Linux Kernel Development (V4L2, DMA, PCIe)
- Debugging proprietary binary-blob integrations
If you have the hardware and the expertise to help rescue this driver, please reach out via Issues or Pull Requests!

## ⚖️ Disclaimer

**This is an unofficial, experimental patch. Use at your own risk.**
This project is an ongoing attempt to make the GC573 functional on modern Linux systems.

---
*Maintained by the community. Credits to derrod.*
