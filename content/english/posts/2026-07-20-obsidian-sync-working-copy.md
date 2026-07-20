---
title: "How to Sync Obsidian Between iPhone/iPad and PC (Working Copy + iOS Shortcuts)"
date: 2026-07-20T23:40:00+09:00
draft: false
---

This manual guides you through setting up a secure and reliable sync environment for your Obsidian Vault between your PC (Mac/Windows) and iOS devices (iPhone/iPad) using GitHub.

The iOS version of the "Obsidian Git" plugin suffers from a critical issue where the Obsidian app itself **crashes (forces closes)** during cloning or Git operations on larger vaults, due to memory limitations of its internal Git library (libgit2).

To avoid these crashes and synchronization errors, this method combines the native iOS Git client **"Working Copy"** (which handles large repositories smoothly) with **"iOS Shortcuts"** to physically overwrite and update your files, establishing a crash-free and robust sync environment.

---

## 🏗️ Architecture

```text
[💻 PC (Mac / Windows)]
       ▲ 
       │ (Auto Sync: Obsidian Git)
       ▼
[🐙 GitHub (Private Repo)]
       │
       │ ① (Git Pull)
       ▼
[📦 Working Copy App (iOS)]
       │
       │ ② (iOS Shortcut: Overwrite & Copy)
       ▼
[📱 Obsidian App (iOS Local)]
```

* **PC-Side**: The "Obsidian Git" plugin automatically commits, pushes, and pulls changes (fully automated).
* **Mobile-Side (iOS)**: The "Working Copy" app pulls the latest changes from GitHub. An iOS Shortcut is then triggered to copy the entire repository directory into Obsidian's local storage (`On My iPhone > Obsidian`).
  * **Benefit 1 (Crash Elimination)**: It completely resolves the app-crashing issues caused by the mobile plugin's memory limits, allowing you to manage large vaults with thousands of files without any performance hit.
  * **Benefit 2 (Reliable Synchronization)**: While Working Copy's native "folder sync" (Link Directory to Repository) feature occasionally disconnects or triggers sync conflicts (like detached HEAD) due to iOS file provider quirks, using an iOS Shortcut to "physically overwrite and copy the folder" ensures your latest notes are reliably reflected in Obsidian on startup.

---

## 🛠️ Prerequisites

1. **GitHub Repository** (Private recommended)
2. **Obsidian (PC)** with the **Obsidian Git plugin** installed
3. **Obsidian (iOS)**
4. **Working Copy (iOS App)**
   * The free version is sufficient for pulling (read-only sync).
   * The Pro version (paid addon) is required if you want to push changes from mobile back to GitHub.
5. **Sync iOS Shortcut**
   * 👉 [Sync vault for sharing (iCloud Shortcut Link)](https://www.icloud.com/shortcuts/507d0c928b4949cb9c01fcbcf74ce15f)

---

## 💻 PC-Side Setup

Configure the "Obsidian Git" plugin on your PC for fully automated synchronization.

1. **Install Obsidian Git**
   * Open Obsidian Settings > Community Plugins, search for **Obsidian Git**, and install/enable it.
2. **Configure Settings**
   * Customize the following based on your preference:
     * **Vault backup interval (minutes)**: e.g., `10` (frequency of auto commit & push)
     * **Auto pull interval (minutes)**: e.g., `10`
     * **Run git pull on startup**: `On` (ensures you get the latest changes immediately)

---

## 📱 Mobile-Side (iPhone/iPad) Setup

Follow these 4 steps to set up sync on your iOS device. You do not need to create an empty Vault beforehand.

### Step 1: Clone the GitHub Repository in Working Copy
1. Open the **Working Copy** app on your iOS device.
2. Tap the **"+"** icon in the top right corner and select **"Clone repository"**.
3. Enter your GitHub repository URL and authenticate using your GitHub account (Personal Access Token or SSH Key) to clone it.
   * Once finished, the repository (e.g., `your-vault-name`) will appear in Working Copy.

### Step 2: Import and Configure the iOS Shortcut
Import the pre-configured sync shortcut and adjust the parameters for your repository.

1. On your iOS device, tap the link below to import the shortcut:
   * 👉 [Sync vault for sharing (iCloud Shortcut Link)](https://www.icloud.com/shortcuts/507d0c928b4949cb9c01fcbcf74ce15f)
2. Open the iOS **Shortcuts** app and tap the three dots `...` on the **"Sync vault for sharing"** shortcut to edit it.
3. Configure the following 3 actions:

   * **① "Pull from Repository" Action**:
     * Tap **"Repository"** and select **your cloned repository (e.g., `your-vault-name`)** from the list.
     * Keep `Remote` as `Default` (or `origin`).
   * **② "Get File" Action** (labeled "ファイルをひらく" in Japanese):
     * Tap the folder icon/name and select your **repository folder (e.g., `your-vault-name`)** inside Working Copy.
   * **③ "Save File" Action** (labeled "ファイルを保存" in Japanese):
     * Set the destination directory to **`On My iPhone › Obsidian`** (or `On My iPad › Obsidian`).
     * Expand the action details and make sure **"Overwrite File"** is toggled **ON** (enabled).

4. Run the shortcut manually once by tapping the play button to verify there are no errors. Check the Files app under `On My iPhone > Obsidian > <Your Repository>` to confirm the files have been copied successfully.

### Step 3: Open the Synced Folder in iOS Obsidian
1. Open the **Obsidian** app on your iOS device.
2. Tap **"Open folder as vault"**.
3. Select the synced repository folder (e.g., `your-vault-name`) inside `On My iPhone/Obsidian/`.
4. Your vault is now open and populated with your notes on mobile.

### Step 4: Prevent Conflicts in iOS Obsidian
To prevent the "Obsidian Git" plugin settings synced from your PC from running on mobile and causing errors, disable it on this device:

1. In iOS Obsidian, go to Settings > Community Plugins > **Obsidian Git**.
2. Toggle **ON** **"Disable on this device"**.
   * This safely disables background Git processes on mobile, as all Git operations are now handled by Working Copy.

---

## 🔄 Setting Up Automation

Configure iOS to automatically run the sync shortcut whenever you open Obsidian so your notes are always up to date.

1. Open the iOS **Shortcuts** app and navigate to the **"Automation"** tab at the bottom.
2. Tap **"New Automation"** (or the "+" icon in the top right).
3. Select **"App"** as the trigger:
   * **App**: Choose `Obsidian`.
   * Check **"Is Opened"**.
   * Select **"Run Immediately"** (to bypass execution confirmation prompts).
4. Select **"Sync vault for sharing"** as the shortcut to run.

Now, every time you open Obsidian, the shortcut will pull the latest changes from GitHub via Working Copy and overwrite your local Obsidian directory in the background.

---

## 🚨 Troubleshooting

* **Q: I did a Force Push on my PC, and now Pull fails on mobile.**
  * **Solution**: Working Copy will reject pulls when git histories diverge (non-fast-forward). The easiest and safest fix is to delete the repository inside the Working Copy app and clone it again from GitHub (repeat Step 1).
* **Q: How can I edit notes on mobile and sync them back to PC? (Two-way sync)**
  * **Solution**: You must purchase the Pro version of Working Copy. Once upgraded, create a second shortcut for "Commit & Push" (using Working Copy's *Commit* with modified files and *Push* actions). Then, set up a new iOS Automation triggered when **"Obsidian is Closed"** to run this Commit & Push shortcut.
