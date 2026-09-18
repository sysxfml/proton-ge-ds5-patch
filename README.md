### Description
This repository provides an automated CI/CD pipeline using GitHub Actions to track upstream GE-Proton releases, inject DualSense (DS5) native haptic feedback and adaptive trigger patches, and publish the compiled custom builds. 

Specifically, this automated builder performs the following steps to achieve its goal:
1. **Fetches** the latest official GE-Proton source code.
2. **Strips out** the newer DualSense controller-related patches and logic from the newly fetched package.
3. **Injects** custom patch replacements based on the legacy GE-Proton 11-1 logic.
4. **Recompiles** the build specifically to support DS5 controller haptic and adaptive trigger features for **non-Steam games running inside Gamescope**.

### Credits & Disclaimer
This is an unofficial, community-driven project. All credits for the core software and patches go to their respective original authors:
*   **GloriousEggroll**: Maintainer of the upstream GE-Proton project. (Project URL: https://github.com/GloriousEggroll/proton-ge-custom)
*   **xzn**: Author of the original DualSense audio and registry bypass patches. (Original Project URL: https://github.com/xzn/proton-ds5-haptic) To ensure the automated build process functions correctly and to preserve the patches, the `.patch` files are hosted within the `ds5-patches/` directory of this repository.

**⚠️ IMPORTANT DISCLAIMER:** 
*   **Personal Use Only:** This custom build and its invasive patches are strictly for **personal use**. Anyone else choosing to use this build does so entirely at their own risk.
*   **Limited Testing:** This compiled version has **ONLY** been tested and verified to work with the game ***Arknights: Endfield* (明日方舟：终末地)** non-steam version. There is absolutely no guarantee that it will function correctly with any other games. 
*   **No Official Support:** **DO NOT** report any bugs, crashes, or issues encountered while using this build to the official Valve Proton bug tracker or the upstream GE-Proton issue tracker. Upstream developers are not responsible for supporting this modified version.

### How to Use
1. Navigate to the [Releases](../../releases) page of this repository.
2. Download the latest `.tar.gz` archive (e.g., `GE-ProtonX-XX-ds5.tar.gz`).
3. Extract the downloaded archive into your Steam compatibility tools directory:
   * **Native/SteamOS:** `~/.steam/root/compatibilitytools.d/`
   * **Flatpak:** `~/.var/app/com.valvesoftware.Steam/data/Steam/compatibilitytools.d/`
4. Restart the Steam client.
5. Right-click your game in Steam -> **Properties** -> **Compatibility** -> Check "Force the use of a specific Steam Play compatibility tool" and select the `-ds5` version.
6. **Important:** You MUST disable Steam Input in the game's controller properties for native haptics to work.

### Troubleshooting
**Touchpad registering as a mouse**
When Steam Input is disabled to allow native DS5 haptics, the Linux `hid-playstation` kernel driver may exposes the controller's touchpad as a standard mouse pointer, which causes unwanted mouse inputs in Gamescope/KDE. 

To fix this, disable the touchpad's mouse functionality via udev rules. Run the following commands in your terminal:

```bash
echo 'KERNEL=="event*", ATTRS{name}=="*Wireless Controller Touchpad*", ENV{LIBINPUT_IGNORE_DEVICE}="1"' | sudo tee /etc/udev/rules.d/99-ignore-ds5-touchpad.rules
sudo udevadm control --reload-rules && sudo udevadm trigger
