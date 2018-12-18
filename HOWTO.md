# How to do all this?

These steps worked for me, there might be better ways of doing it.

## Device Tree Adjustments
1. Until I've figured out the device tree business on linux (and how to do overlays, etc.), I do things manually.
2. You therefore need to adapt the dts/dtc file also so that SPI is accessible to the lego driver and is available on the J21 header of the Jetson TX2. Specifically, the file to change is /boot/dtb/tegra186-quill-p3310-1000-c03-00-base.dtb.

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
3. Once this is done you can move to the next steps (software) + hardware.

## Software steps
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

## Hardware steps
1. Get the BrickPi3 connected to the Jetson TX2 using the J21 header.... but you need to inverse pins 24 and 26, which is the chipselect of SPI. BrickPi3 expects CS1 (pin26) and the Jetson seems to only want to do CS0 (pin24).
2. I've used the signals for : +3.3V (make sure the jumper on the Jetson is set to 3.3V and not 1.8V), +5.0V, MISO, MOSI, CLK, CS0 and 2 GND wires. 
3. I've only used the pins for SPI and left the I2C and SDA pins alone for now as I didn't need them for my projects. Happy to get contributions for that part, and merge changes that enable and test this. If I need to run firmware updates, I'll wire the BrickPi3 to the Raspberry PI and back on the Jetson.
4. Boot it up and it should just work.
