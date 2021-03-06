# Tock Embedded OS [![Build Status](https://travis-ci.org/helena-project/tock.svg?branch=master)](https://travis-ci.org/helena-project/tock)

Tock is an operating system designed for running multiple concurrent, mutually
distrustful applications on Cortex-M based embedded platforms. Tock's design
centers around protection, both from potentially malicious applications and
from device drivers. Tock uses two mechanisms to protect different components
of the operating system. First, the kernel and device drivers are written in
Rust, a systems programming language that provides compile-time memory safety,
type safety and strict aliasing. Tock uses Rust to protect the kernel (e.g. the
scheduler and hardware abstraction layer) from platform specific device drivers
as well as isolate device drivers from each other. Second, Tock uses memory
protection units to isolate applications from each other and the kernel.

## Requirements

1. [Rust](http://www.rust-lang.org/) (nightly)
2. [arm-none-eabi toolchain](https://launchpad.net/gcc-arm-embedded/) (version >= 4.9)
3. stormloader (recommended) or JLinkExe for programming the storm
4. Command line utilities: wget, sed, make

### Installing Requirements

### Rust (nightly)

We are using `rustc 1.12.0-nightly (54c0dcfd6 2016-07-28)`. We recommand
installing it with [rustup](http://www.rustup.rs) so you can manage multiple
versions of Rust and continue using stable versions for other Rust code:

```bash
$ curl https://sh.rustup.rs -sSf | sh
```

Then override the default version of Rust to use for Tock by running the
following from the top-level Tock directory:

```bash
$ rustup override set nightly-2016-07-29
```

#### `arm-none-eabi` toolchain

We are currently using arm-none-eabi-gcc version 5.4 from the gcc-arm-embedded
PPA on lauchpad. Using pre-5.0 versions from that repo, or other versions
packaged with a newlib version earlier than 2.3 will run into problems with
missing ARM intrinsics (e.g., ```__aeabi_memclr```).

On Mac OS X, you can get the arm-none-eabi toolchain via port:

```bash
$ port install arm-none-eabi-gcc
```

or via homebrew:

```bash
$ brew tap PX4/homebrew-px4
$ brew update
$ brew install gcc-arm-none-eabi
```

On Linux we recommend getting packages from Launchpad

https://launchpad.net/gcc-arm-embedded/+download

E.g.:

```bash
$ curl https://launchpad.net/gcc-arm-embedded/5.0/5-2016-q2-update/+download/gcc-arm-none-eabi-5_4-2016q2-20160622-linux.tar.bz2
```

On Arch Linux the `arm-none-eabi` package in pacman contains a sufficiently up
to date version of newlibc.

For Windows and other operating systems, download site is
[here](https://launchpad.net/gcc-arm-embedded/+download).

Alternatively, if you would like simulator mode in `arm-none-eabi-gdb`,
you can use the build scripts in the `tools` directory, in this order:
`build-arm-binutils` then `build-arm-gcc` then `build-arm-gdb`.

## Building the Kernel

If all the tools are in your `$PATH`, you should be good to go. Otherwise set the env variables:

* `RUSTC` - `rustc` compiler
* `CC` - `arm-none-eabi-gcc`
* `OBJCOPY` - `arm-none-eabi-objcopy`

The build system respects the environment variable `TOCK_PLATFORM`, which
currently defaults to the `storm` platform but can be set to any available
platform (anything in `src/platform`).

Invoking `make` in the root directory will build the tock kernel (placing it
in `tock/build/$(TOCK_PLATFORM)/kernel.o`).

## Building apps

To build applications, change to `apps/$(APP)/` directory and invoke `make`.
This will build the app and generate a binary in Tock Binary Format (using the
`elf2tbf` utility) in `build/$(PLATFORM)/$(APP)/$(APP).bin`. Depending on the
platform, this binary should either be programmed separately from the kernel,
or linked into it directly and programmed together. See the README file in each
platform subdirectory for details.

