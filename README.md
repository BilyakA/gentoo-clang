# Gentoo-Clang overlay
An overlay for Gentoo, providing patches for upstream Gentoo repository to make it possible to build entire @world with clang compiler.

## Quick start
First of all you need to build clang with clang (self-host). The easiest way is to install clang:5 and then to build clang:6 (or clang:7 testing) with it.

```
emerge -av1 clang:5 llvm:5
```
Now add the following to your make.conf (just like [wiki](https://wiki.gentoo.org/wiki/Clang#Global_configuration_via_make.conf) says):

> **Note:** It is important that you define full path to clang's binaries or at least the version of clang's binaries, as compilation may fail somewhere in the middle because some binaries would be updated with LLVM package and others - with clang package.

```
CC="/usr/lib/llvm/5/bin/clang-5.0"
CXX="/usr/lib/llvm/5/bin/clang++-5.0"
CFLAGS="-O2 -pipe -march=native"
CXXFLAGS="${CFLAGS}"
LDFLAGS="-Wl,-O2 -Wl,--as-needed"
AR="/usr/lib/llvm/5/bin/llvm-ar"
NM="/usr/lib/llvm/5/bin/llvm-nm"
RANLIB="/usr/lib/llvm/5/bin/llvm-ranlib"
```
Now you can self-host LLVM/Clang (remember to remove clang:5 after you've done and change your make.conf to new version)
```
emerge -av clang:6 llvm:6
emerge --depclean -a
```
Change your make.conf to new version of clang:
```
CC="/usr/lib/llvm/6/bin/clang-6.0"
CXX="/usr/lib/llvm/6/bin/clang++-6.0"
CFLAGS="-O2 -pipe -march=native"
CXXFLAGS="${CFLAGS}"
LDFLAGS="-Wl,-O2 -Wl,--as-needed"
AR="/usr/lib/llvm/6/bin/llvm-ar"
NM="/usr/lib/llvm/6/bin/llvm-nm"
RANLIB="/usr/lib/llvm/6/bin/llvm-ranlib"
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

## Notes
### libunwid
In general it is good idea to switch from `sys-libs/libunwind` to `sys-libs/llvm-libunwind`. They are blocking each other so just `emerge --unmerge sys-libs/libunwind && emerge -av1 sys-libs/llvm-libunwind`.
### libcxx
If we are getting rid of GCC and moving forward LLVM and Clang, so why not switch to libc++ as STL?
Just add `default-libcxx` for clang and `libcxx` for all dev-qt to your USE.
### GCC fallback
Even if we are aiming to completely replace GCC with Clang, it is not possible yet to compile everything with it. So make sure you have GCC fallback environment set up like [wiki](https://wiki.gentoo.org/wiki/Clang#GCC_fallback_environments) describes.

## Issues and bug reports
Definitely I'll do my best to cover as much packages as possible and to provide patches ASAP. Nevertheless [reporting of broken packages via issue](https://github.com/BilyakA/gentoo-clang/issues/new) and pull requests are welcome!
