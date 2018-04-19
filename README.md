# py-uio
Userspace I/O in Python

All examples currently target the BeagleBone family of devices.

This library and its examples are for interfacing with uio devices, in
particular those using the `uio_pdrv_genirq` driver and those using the
`uio_pruss` driver.

There isn't much documentation yet other than this README, but a little bit can
be found on [the wiki](https://github.com/mvduin/py-uio/wiki).

## uio_pruss

Copy the [uio-pruss.rules](etc/udev/rules.d/uio-pruss.rules) file to
`/etc/udev/rules.d/`, then run `sudo udevadm control -R` and `sudo udevadm
trigger -s uio`, or just reboot.  This creates symlinks (in `/dev/uio/`) to
allow the uio devices to be located easily.

Now you can try out the examples:
 * [pruss-test.py](pruss-test.py) is a minimalistic example that initializes register R0 of a pru core to 123, loads and executes a [tiny pru program](pruss-test-fw.pasm) that increments R0, and then reads back and prints R0 (which should therefore print 124).
 * [pruss-ddr-ping.py](pruss-ddr-ping.py) is a small test of using a shared DDR3 memory region.
 * [pruss-elf-test.py](pruss-elf-test.py) demonstrates how to load an ELF executable produced by clpru.
 * [pruss-intc-test.py](pruss-intc-test.py) is a more involved example that showcases sharing a data structure (in pruss local memory) between python code and the PRU cores, and sending events from both pru cores via the pruss interrupt controller to event handlers in python.

## uio_pdrv_genirq

(TODO: this is outdated information and needs to be revised!)

Copy the stuff in the [etc/](etc/) dir to the corresponding places in `/etc`
and tweak the udev rule to taste (user/group/permissions).

The [dts/](dts/) dir contains example device tree fragments.  If you use a
custom dts then you can simply include such dtsi files, but since most people
don't you can also type `make` to build device tree overlays from them and use
the utils in [dts/bin/](dts/bin/) to add/remove them.

Example 1 (gpio-triggered IRQ):
```bash
cd dts
make gpio-irq.dtbo
sudo bin/add-overlay gpio-irq.dtbo
cd ..
./gpio-irq.py
# now pull P9.12 to ground to trigger the irq the script is waiting for
```

Example 2 (small experiment with L3 service network):
```bash
cd dts
make l3-sn.dtbo
sudo bin/add-overlay l3-sn.dtbo
cd ..
./l3-sn-test.py
```

The l3-sn is useful testing ground since it is very fussy about access size.
