# How to do all this?

These steps worked for me, there might be better ways of doing it.

1. Download the kernel sources from NVIDIA for version of kernel 4.4.38. I didn't push it here, I'm not sure if the license allows to. You'll get that from L4T 28.2.1.
2. Clone this repo, recursively, into /kernel/kernel-4.4/. Recursively will include the drivers/lego directory which points to this repo. You can point to the original repo too (on [ev3dev](https://github.com/ev3dev/lego-linux-drivers)) as I didn't do any change except the .localversion file which identifies is compiled kernel as running ev3dev.
3. Extract the relevant files (see the [JetsonHacks article](https://www.jetsonhacks.com/2018/07/05/jetson-tx2-build-kernel-for-l4t-28-2-1-updated/)), OVER the files cloned in the previous step.
4. Stash the changes (since the files cloned from the repo should superceed the ones in the tar archive of the kernel - patches for one file and a few things like the .config file for kernel configuration).
5. Run the compilation (make prepare, make modules_prepare, make Image, make modules)
6. Copy the image to /boot/Image (maybe take a backup of your current image)
7. Copy install the modules (make install_modules)
8. Copy the file from the [ev3dev-rules](https://github.com/ev3dev/ev3dev-rules/blob/ev3dev-stretch/debian/ev3dev-rules.ev3dev.udev) to the directory /lib/udev/rules.d/ and give it a name like 60-ev3dev.rules. The name of the file as in the repo won't cut it, the udev engine won't read it, it needs a .rules extension as per the man page for udev.
9. Reboot.
10. You can at this point pull a docker repo (I'll assume you've got Docker installed), and run ev3dev from there. I've pulled this one and it looks like it's working so far for me : docker pull ev3dev/ev3dev-stretch-rpi2-generic:2018-12-14. Maybe the latest tag will work, and it should.
