## Calibrated Interrupts
This repository contains instructions and source code for compiling and
reproducing results in the Calibrated Interrupts (cinterrupts) paper,
to appear in OSDI '21 (see the paper PDF in the top-level of this repository).

### Evaluation instructions
At the end of this document, we describe how to compile and install
the evaluation environment, should the evaluator choose to do so.
However, due to needing the cinterrupts custom kernel, we have set up
an environment for the evalutors on our machines.

#### Accessing the evaluation environment
TODO: information on how to access the machines.


### Content of the repository
* `linux-kernel` directory with Linux kernel sources and cinterrupts patch
* `linux-kernel/cinterrupts.patch` device emulation and nvme driver
* `linux-kernel/linux-kernel-5.0.0-16.17.tgz-part-a[abcd]` splitted archive of the Linux vanilla kernel ver 5.0.0-16.17
* `linux-kernel/config-file` config file used for our kernel compilation
* `build-kernel.sh` script to extract Linux kernels source, apply the cinterrupts patch and compile the kernel
* `fio` directory with fio 3.13 sources and cinterrupt patch for fio
* `fio/fio-3.13.tgz` sources of original version of fio 3.13
* `fio/fio_barrier.patch` patch with cinterrupts support in fio
* `build-fio.sh` script to extract fio source, apply cinterrupts patch and compile the fio
* `utils` utilities and helper scripts we use in our project
* `fig5` directory with scripts to reproduce Figure 5 in the paper




#### Reproducing each figure
In the XXX/ subdirectory, we have scripts and instructions for reproducing the key figures in our paper.


### Compilation instructions
We highly recommend that you build on Ubuntu 16.04.
To build the custom cint kernel, you will need any dependencies required for the Linux kernel.
These include libssl-dev, bison, flex, and optionally dh-exec.
If there is a compilation error, it is likely because one of these packages is missing.

Run `build-kernel.sh` in the top-level directory of this repository.
This will build and install this custom kernel in the normal places,
i.e. /boot and update grub. The name of the kernel image
will be 5.0.8-nvmecint. You will then need to reboot into this kernel,
which is only necessary the first time.

When kernel is loaded the driver is ready. If you modify the driver and
need to compile it then run:


```
$> cd linux-kernel/linux-kernel-5.0.0-16.17
$> sh nvme-make.sha

```

After that, to switch between different NVMe interrupt emulations and
the original driver, you simply need to unload and load the correct
nvme driver with relevant parameters:

```
$> cd linux-kernel/linux-kernel-5.0.0-16.17
$> sh ./nvme-reload.sh our-sol
$>
$> sh ./nvme-reload.sh
Usage: ./nvme-reload.sh {orig|emul|our-sol}

     orig    -- original nvme driver, for-bare-metal tests
     emul    -- emulation of the original nvme driver on a side core
     emul-100-32 -- emulation of the original nvme driver with 100 usec and 32 thr aggregation params
     our-sol -- side-core emulation of our nvme prototype with URGENT and BARRIER flags
     alpha   -- side-core emulation of our nvme prototype, only adaptive coalescing
     alpha0  -- side-core emulation of our nvme prototype, without any thresholds (new baseline0)
```

To change the parameters edit config files in form `nvme-$(hostname)-$(mode).conf`, for example:

```
$> cd linux-kernel/linux-kernel-5.0.0-16.17
$> vim nvme-$(hostname).conf            # params for the cinterrupts driver
$> vim nvme-$(hostname)-clean.conf      # params for the original nvme driver
$> vim nvme-$(hostname)-emul.conf       # params for the emulated nvme device driver

```


### Installation and Setup Instructions
After booting into this custom kernel, compile fio benchmark.
Run `build-fio.sh` in the top-level directory of this repository.
Path to fio from the top-level directory: `fio/fio-3.13/fio`
If you can successfully run FIO, you are ready!

Now you can run the following experiments:
TODO: decribe each experiment

### Running benchmarks
You are welcome to clone and compile the following applications, which are applications we modified for cinterrupts.

- FIO
- RocksDB
- KVell
