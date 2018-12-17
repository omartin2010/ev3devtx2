Project for the EV3DEV drivers. This is the dev branch where the work happens. Work will be moved to master when something is somewhat functional. 
=======
First working version is with kernel 4.4.38, and complete version is 4.4.38-ev3dev-xenial-tx2-2.3.1+.
Image is included, once you have it you can copy it and replace /boot/Image... and make sure to adapt dts/dtc file also so that the SPI device is accessible to the lego driver.
Specifically, the file to change is /boot/dtb/tegra186-quill-p3310-1000-c03-00-base.dtb.

Look for the section on SPI and some articles on how to enable SPI on the Jetson TX2.

```
 spi@3240000 {
  reg = <0x0 0x3240000 0x0 0x10000>;
  dmas = <0x19 0x12 0x19 0x12>;
  interrupts = <0x0 0x27 0x4>;
  compatible = "nvidia,tegra186-spi";
  clock-names = "spi", "pll_p", "clk_m";
  reset-names = "spi";
  clocks = <0xd 0x4a 0xd 0x10d 0xd 0x261>;
  spi-max-frequency = <500000> ;     # NOT SURE IF IT REALLY NEEDS TO BE IN THE PARENT DEVICE
  nvidia,dma-request-selector = <0x19 0x12>;
  resets = <0xd 0x2b>;
  status = "okay";
  #address-cells = <0x1>;
  phandle = <0x7d>;
  nvidia,clk-parents = "pll_p", "clk_m";
  #stream-id-cells = <0x1>;
  #size-cells = <0x0>;
  dma-names = "rx", "tx";
  linux,phandle = <0x7d>;
    spi@0 {      # THIS SPI DEVICE NEEDS TO BE ADDED AND MOST THINGS BELOW.
      compatible = "ev3dev,brickpi3","spidev";
      reg = <0>;
      spi-max-frequency = <500000>;  
      nvidia,enable-hw-based-cs;
      nvidia,cs-setup-clk-count = <0x1e>;
      nvidia,cs-hold-clk-count = <0x1e>;
      nvidia,rx-clk-tap-delay = <0x1f>;
      nvidia,tx-clk-tap-delay = <0x0>;
    };
```
Special thanks to [@dlech](https://github.com/dlech) and [@WasabiFan](https://github.com/WasabiFan] from the [ev3dev project](http://www.ev3dev.org), as well as [@mattallen37](https://github.com/mattallen37) from [Dexter Industries](http://dexterindustries.com). And also to [JetsonHacks](https://www.jetsonhacks.com/2018/07/05/jetson-tx2-build-kernel-for-l4t-28-2-1-updated/) for the inspiration and mostly this particular article on how to locally compile, on the device (which is what I've done), the kernel in order to configure it so that it supports in the first place, SPI and then BrickPi3.

TODO, among other things
- [ ] Eventually configure the device tree with overlays so that it can be more automated.
- [ ] Maybe generate an image. In fact, the Image file is there (so you can copy it to /boot/Image) but you need to make sure the kernel, as you compile it, gets the files from this repo pulled on it.
- [ ] Probably a bunch more.
