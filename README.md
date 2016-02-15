Introduction
============

This contains the modified little-endian ARM kernel for the FIRMADYNE framework,
which includes an in-tree `firmadyne` module to perform instrumentation and
emulation.

This module can be configured using the following parameters:

| Parameter | Default   | Values | Description |
| --------- | --------- | ------ | ----------- |
| devfs     | 1 (on)    | 0, 1   | Create stubs in devfs and emulate behavior |
| execute   | 1 (on)    | 0 - 5  | Counter to execute `/firmadyne/console` after 4th `execve()` syscall (requires syscall hooks), 0 to disable |
| reboot    | 1 (on)    | 0, 1   | Attempt to emulate system reboot by re-executing `/sbin/init` |
| procfs    | 1 (on)    | 0, 1   | Create stubs in procfs and emulate behavior |
| syscall   | 255 (all) | 0 - 16 | Output log bitmask for hooking system calls using the `kprobe` framework, 0 to disable |

Usage
=====

Create the kernel build output directory:

`mkdir -p build/armel`

Copy the configuration file into the build directory:

`cp config.armel build/armel/.config`

Assuming that the appropriate cross-compiler is installed in `/opt/cross/arm-linux-musleabi`, execute:

`make ARCH=arm CROSS_COMPILE=/opt/cross/arm-linux-musleabi/bin/arm-linux-musleabi- O=./build/armel zImage -j8`

The output kernel image will be generated at the following location:

`build/armel/arch/arm/boot/zImage`

Notes
=====

This instrumented ARM kernel is built for the `ARCH_VEXPRESS` [ARM Versatile
Express](http://wiki.qemu.org/download/qemu-doc.html#ARM-System-emulator)
target with a Cortex-A9 processor. As a result, hardware support on this
emulated target is limited to peripherals that are both available on the
emulated target and supported by QEMU. This means that at most one SMC91C11
ethernet adapter, and only the PL181 MMC SD block device interface are
supported. The only ARM targets that support an emulated PCI bus in QEMU, which
allows emulation of additional ethernet devices, are the RealView Platform
(`realview-pb*`) and Versatile Platform Baseboard (`versatilepb`) targets.
However, the ARM926EJ-S on the Versatile Platform Baseboard only supports up to 
the ARMv5 instruction set (not ARMv6/ARMv7-A, nor Thumb/Thumb-2), and we have 
been unable to successfully boot an emulated RealView v3.10 kernel.

As a result, this kernel is deprecated in favor of
[kernel-v4.1](https://github.com/firmadyne/kernel-v4.1), which utilizes
[VirtIO](http://wiki.libvirt.org/page/Virtio) to support more virtualized
devices. Nevertheless, at the time, we performed our published experiments
over our dataset using this kernel for ARM systems.

Although the ARM platform is bi-endian, currently upstream QEMU does not support
any emulated big-endian ARM targets. As future work, it may be useful to
investigate alternative forks of QEMU that do support big-endian ARM, such as
the [Xilinx fork](https://github.com/Xilinx/qemu) for BE8. Additionally, it may
be useful to add support for 64-bit ARM (AArch64) systems, which may grow
increasingly prevalent.

Pull requests are greatly appreciated!
