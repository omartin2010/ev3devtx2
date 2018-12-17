# How to do all this?

These steps worked for me, there might be better ways of doing it.

1. Download the kernel sources from NVIDIA for version of kernel 4.4.38. I didn't push it here, I'm not sure if the license allows to. You'll get that from L4T 28.2.1.
2. Clone this repo, recursively, into /kernel/kernel-4.4/. Recursively will include the drivers/lego directory which points to this repo. You can point to the original repo too (on [ev3dev](https://github.com/ev3dev/lego-linux-drivers)) as I didn't do any change except the .localversion file which identifies this compiled kernel as running ev3dev.
3. Extract the relevant files (see the [JetsonHacks article](https://www.jetsonhacks.com/2018/07/05/jetson-tx2-build-kernel-for-l4t-28-2-1-updated/)), OVER the files cloned in the previous step.
4. Stash the changes (since the files cloned from the repo should superceed the ones in the tar archive of the kernel - patches for one file and a few things like the .config file for kernel configuration).
5. Run the compilation.
