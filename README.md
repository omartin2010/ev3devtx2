Project for the EV3DEV drivers. This is the dev branch where the work happens. Work will be moved to master when something is somewhat functional. 
=======
First working version is with kernel 4.4.38, and complete version is 4.4.38-ev3dev-xenial-tx2-2.3.1+.
Image is included, once you have it you can copy it and replace /boot/Image... 
Make sure to look at the [HOWTO](howto.md) article. 
Special thanks to [@dlech](https://github.com/dlech) and [@WasabiFan](https://github.com/WasabiFan) from the [ev3dev project](http://www.ev3dev.org), as well as [@mattallen37](https://github.com/mattallen37) from [Dexter Industries](http://dexterindustries.com). And also to [JetsonHacks](https://www.jetsonhacks.com/2018/07/05/jetson-tx2-build-kernel-for-l4t-28-2-1-updated/) for the inspiration and mostly this particular article on how to locally compile, on the device (which is what I've done), the kernel in order to configure it so that it supports in the first place, SPI and then BrickPi3.

TODO, among other things
- [ ] Eventually configure the device tree with overlays so that it can be more automated.
- [ ] Maybe generate an image. In fact, the Image file is there (so you can copy it to /boot/Image) but you need to make sure the kernel, as you compile it, gets the files from this repo pulled on it.
- [ ] Probably a bunch more.
