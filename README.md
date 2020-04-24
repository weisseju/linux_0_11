# linux_0_11
linux 0.11 boots into bash


# Linux-0.11

The old Linux kernel source ver 0.11 which has been tested under modern Linux, macOS, Windows and Android.

## modern Linux Setup

* Install gcc, gdb, binutils, qemu, bochs and VSCode
* Install VSCode C/C++ extension

## Windows 10 WSL Setup

* Upgrade Windows 10 to version 1903(19H1) or latter.
* Enable WSL ([Windows subsystem of Linux](https://docs.microsoft.com/windows/wsl)), download a Linux distro from Microsoft store or Github. install it.
* Install [VSCode](https://code.visualstudio.com/) and install **Remote Development** extension.
* Install make, gcc, gdb and binutils in **wsl**
* Install qemu, bochs in **windows**
* Open new **wsl window** in vscode (see [docs for Remote-wsl](https://aka.ms/vscode-remote/wsl/getting-started)), install C/C++ extension on **wsl**
* Add all tools to `PATH`, so you can excute them directly from wsl shell.
* Run all command in **wsl shell**.(eg. `make`)

### Access file in wsl

* You can access wsl's Linux files in Explorer(or any win32 app) at `\\wsl$\Ubuntu` (or you distro name)
* You can access Windows's files in wsl at `/mnt/c` (or other drive letter)

### Known issue

* You can't mount minix image file in wsl1. wsl2 can work.

## Windows Setup

* Install `git`
* [download](https://sourceforge.net/projects/ezwinports/files/make-4.2.1-without-guile-w32-bin.zip/download) prebuilt GNU `make` for Windows. Extract `bin/make.exe` to somewhere.(eg. `C:\Program Files\Git\bin`)
* [download](https://github.com/lordmilko/i686-elf-tools/releases) prebuilt GNU `i686-elf` toolchain for Windows. Extract to somewhere.(eg. `C:\i686-elf\`)
* Install [qemu](https://qemu.weilnetz.de/), [bochs](https://sourceforge.net/projects/bochs/files/bochs/2.6.9/Bochs-2.6.9.exe/download)
* Install [VSCode](https://code.visualstudio.com/)
* Install VSCode C/C++ extension
* Add all tools to `PATH`, so you can excute them directly from `Git Bash` shell.
* Modify VSCode C/C++ configuration in `.vscode`,set proper path for gcc and gdb.
* Run all command in `Git Bash` shell.(eg. `make`)

## macOS Setup

* Install Xcode command line tools: `xcode-select --install`
* Install [Homebrew](https://brew.sh/)
* Install i386-elf cross compiler and toolchain: gcc, gdb and binutils
* Install qemu, bochs
* Install [VSCode](https://code.visualstudio.com/)
* Install VSCode C/C++ extension
* Configure C/C++ extension

```bash
brew install qemu bochs i386-elf-binutils i386-elf-gcc i386-elf-gdb
# i386-elf-gdb have conflict file with i386-elf-binutils : /usr/local/share/info/bfd.info
brew link --overwrite i386-elf-gdb
```

Apply this diff.

```diff
--- a/.vscode/launch.json
+++ b/.vscode/launch.json
@@ -11,7 +11,8 @@
             "cwd": "${workspaceFolder}",
             "environment": [],
             "MIMode": "gdb",
-            "miDebuggerPath": "gdb",
+            "miDebuggerPath": "i386-elf-gdb",
             "targetArchitecture": "x86",
             "setupCommands": [
                 {
                     "description": "Enable pretty-printing for gdb",
--- a/.vscode/c_cpp_properties.json
+++ b/.vscode/c_cpp_properties.json
@@ -6,7 +6,7 @@
                 "${workspaceFolder}/**"
             ],
             "defines": [],
-            "compilerPath": "/usr/bin/gcc",
+            "compilerPath": "/usr/local/bin/i386-elf-gcc",
             "cStandard": "c89",
             "cppStandard": "c++98",
             "intelliSenseMode": "gcc-x64"
```

### Mount minixfs in macOS

**DON'T WORK NOW**.

You need addition program to mount minix image

* Install [osxfuse](https://osxfuse.github.io/)
* Compile [minixfs](https://github.com/osxfuse/filesystems/tree/master/filesystems-c/unixfs/minixfs)
* You can use `minixfs` to `mount` a minix filesystem. Unfortunately, it don't work `with hdc-0.11.img`.

## Android Setup

You need install [Termux](https://wiki.termux.com/wiki/Main_Page) App.

```bash
# Install Clang and LLVM toolchain to compile kernel
pkg install git clang llvm lldb
```

If you want to run Linux 0.11 on your Android device, see instructions at [Termux wiki](https://wiki.termux.com/wiki/Graphical_Environment) to set up graphical environment.

```bash
pkg install qemu-system-i386
```

Alternatively, you can install `qemu-system-i386-headless` to run in the terminal.

A PR for `Bochs` is available in [termux/x11-packages new package: bochs #143](https://github.com/termux/x11-packages/pull/143)

## setup for Clang and LLVM

You can use Clang and LLVM to compile Linux 0.11 kernel.(Android users only have this option)

Just replace variables in `Makefile.header` with

```makefile
AS = clang --target=i386-pc-none-elf -c
LD = ld.lld
LDFLAGS = -m elf_i386
CC = clang --target=i386-pc-none-elf
CFLAGS = -gdwarf-2 -g3 -m32 -fno-builtin -fno-stack-protector -fomit-frame-pointer
CPP = clang-cpp -nostdinc
AR = llvm-ar
STRIP = llvm-strip
OBJCOPY = llvm-objcopy
NM = llvm-nm
```

macOS users should install `llvm` package from `brew`, as Apple Clang does not have some llvm tools(llvm-objcopy and llvm-ar).

Windows perbuild LLVM package don't have some llvm tools.

## Build and run

```bash
# build
make
# run in qemu
make run
# debug in bochs
make bochs
# open debug server from qemu
make debug
# run gdb command and connect to qemu
make gdb
# run lldb command and connect to qemu
make lldb
# clean files
make clean
# mount hdc-0.11.img to hdc (only on linux and WSL2)
make mount
# umount hdc
make umount
```

### debug kernel in vscode

First run qemu in debug mode

```bash
make debug
```

Press F5 to launch vscode debug
