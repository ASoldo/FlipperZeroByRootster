In this secion we will cover how to clone latest Flipper Zero Firmware and flash out our device using Linux, Flipper Zero and Usb Cabel.

Steps:
1. `git clone https://github.com/flipperdevices/flipperzero-firmware`[^1]
2. `cd fipperzero-firmware`[^2]
3. `git checkout dev`[^3]
4. `git pull`[^4]
5. `./fbt flash_usb_full`[^5]

Now you are ready for your first [[Hello World Application (.fap)]]

### Honorable mention:
[Web Flipper Editor](https://ilin.pt/stuff/fui-editor/assets/editor.html)[^6]

 [^1]: This will download/clone project from github to our local machine on desired destination.
 [^2]: In Terminal, navigate to flipperzero-firmware location on your machine
 [^3]: Switch to `dev` branch since we want to get latest changes
 [^4]: This is just sanity check (good practice)
 [^5]: ./fbt flash_usb will compile all applications and services used by latest flipper zero firmware and when it's done it will upload that output to Flipper Zero device. After that, flipper zero will start flashing process and will be in that state around a minute. Flipper Zero will then be updated and ready to use.
 [^6]: This is free UI editor for your future apps, play with it.
