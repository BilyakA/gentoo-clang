# Gentoo-Clang overlay
An overlay for Gentoo, providing patches for upstream Gentoo repository to make it possible to build entire @world with clang compiler.

## Quick start
First of all you need to build clang with clang (self-host). The easiest way is to install clang:8 and then to build clang:9 (or clang:10 testing) with it.

```
emerge -av1 clang:8 llvm:8
```
Now add the following to your make.conf (just like [wiki](https://wiki.gentoo.org/wiki/Clang#Global_configuration_via_make.conf) says):

> **Note:** It is important that you define full path to clang's binaries or at least the version of clang's binaries, as compilation may fail somewhere in the middle because some binaries would be updated with LLVM package and others - with clang package.

```
CC="/usr/lib/llvm/8/bin/clang"
CXX="/usr/lib/llvm/8/bin/clang++"
AR="/usr/lib/llvm/8/bin/llvm-ar"
NM="/usr/lib/llvm/8/bin/llvm-nm"
RANLIB="/usr/lib/llvm/8/bin/llvm-ranlib"

COMMON_FLAGS="-march=native -O2 -pipe"
CFLAGS="${COMMON_FLAGS}"
CXXFLAGS="${COMMON_FLAGS}"
FCFLAGS="${COMMON_FLAGS}"
FFLAGS="${COMMON_FLAGS}"
```
Now you can self-host LLVM/Clang (remember to remove clang:8 after you've done and change your make.conf to new version)
```
emerge -av clang:9 llvm:9
emerge --depclean -a
```
Change your make.conf to new version of clang:
```
CC="/usr/lib/llvm/9/bin/clang"
CXX="/usr/lib/llvm/9/bin/clang++"
AR="/usr/lib/llvm/9/bin/llvm-ar"
NM="/usr/lib/llvm/9/bin/llvm-nm"
RANLIB="/usr/lib/llvm/9/bin/llvm-ranlib"

COMMON_FLAGS="-march=native -O2 -pipe"
CFLAGS="${COMMON_FLAGS}"
CXXFLAGS="${COMMON_FLAGS}"
FCFLAGS="${COMMON_FLAGS}"
FFLAGS="${COMMON_FLAGS}"
```
Before you start rebuilding entire world remember to include overrided eclasses to your gentoo repository.
Add following to `/etc/portage/repos.conf/gentoo.conf` in `[DEFAULT]` section:
```
eclass-overrides = gentoo-clang
```
Finally you are good to go:
```
emerge -eav1 --with-bdeps=y @world
```

## GCC fallback
Even if we are aiming to completely replace GCC with Clang, it is not possible yet to compile everything with it. So make sure you have GCC fallback environment set up like [Gentoo wiki](https://wiki.gentoo.org/wiki/Clang#GCC_fallback_environments) or [this page](https://github.com/BilyakA/gentoo-clang/wiki/GCC-fallback) describes.
Also you can find (almost) up-to-date list of currently non-supported packages that you should compile with GCC [here](https://github.com/BilyakA/gentoo-clang/wiki/GCC-fallback#list-of-packages-that-require-gcc-fallback).

## Troubleshooting
Refer to [troubleshooting page](https://github.com/BilyakA/gentoo-clang/wiki/Troubleshooting)

## Notes
### libunwid
You could switch from `sys-libs/libunwind` to `sys-libs/llvm-libunwind`. They are blocking each other so just `emerge --unmerge sys-libs/libunwind && emerge -av1 sys-libs/llvm-libunwind`.
### libcxx
If we are getting rid of GCC and moving forward LLVM and Clang, so why not switch to libc++ as STL?
Just add `default-libcxx` for clang and `libcxx` for all dev-qt to your USE.

## Issues and bug reports
Definitely I'll do my best to cover as much packages as possible and to provide patches ASAP. Nevertheless [reporting of broken packages via issue](https://github.com/BilyakA/gentoo-clang/issues/new) and pull requests are welcome!
