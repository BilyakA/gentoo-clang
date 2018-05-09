# Gentoo-Clang overlay
An overlay for Gentoo, providing patches for upstream Gentoo repository to make it possible to build entire world with clang compiler.

## Quick start
First of all you need to build clang with clang. The easiest way is to install clang:4 and then to build clang:5 (or clang:6 testing) with it.

```
emerge -av1 clang:4 llvm:4
```
Now add following to your make.conf (just like [wiki](https://wiki.gentoo.org/wiki/Clang#Global_configuration_via_make.conf) says):
(it is important that you define full path to clang binaries or at least version of clang binaries, as compilation may fail somewhere in the middle because some biniries would be updated with llvm package and others - with clang package)
```
CC="/usr/lib/llvm/4/bin/clang-4.0"
CXX="/usr/lib/llvm/4/bin/clang++-4.0"
CFLAGS="-O2 -pipe -march=native"
CXXFLAGS="${CFLAGS}"
LDFLAGS="-Wl,-O2 -Wl,--as-needed"
AR="/usr/lib/llvm/4/bin/llvm-ar"
NM="/usr/lib/llvm/4/bin/llvm-nm"
RANLIB="/usr/lib/llvm/4/bin/llvm-ranlib"
```
Now you can build clang using clang (remember to remove clang:4 after you've done and change your make.conf to new version)
```
emerge -av clang:5 llvm:5
emerge --depclean -a
```
Change your make.conf to new version of clang:
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
Before you start rebuilding entire world remember to include overrided eclasses to your gentoo repository.
Add following to `/etc/portage/repos.conf/gentoo.conf` in `[DEFAULT]` section:
```
eclass-overrides = gentoo-clang
```
Finnaly you are good to go:
```
emerge -eav1 --with-bdeps=y @world
```

## Notes
### libunwid
In general it is good idea to switch form `sys-libs/libunwind` to `sys-libs/llvm-libunwind`. They are blocking each other so just `emerge --unmerge sys-libs/libunwind && emerge -av1 sys-libs/llvm-libunwind`.
### libcxx
If we are getting rid of GCC and moving forward LLVM and CLang, so why not to switch to libc++ as STL?
Just add `default-libcxx` for clang and `libcxx` for all dev-qt to your USE.
### GCC fallback
Even if we are aiming to completely replace GCC with CLang it is not possible yet to compile everything with clang. So make sure you have GCC fallback environment set up like [wiki](https://wiki.gentoo.org/wiki/Clang#GCC_fallback_environments) describes.

## Issues and bug reports
Definitely I'll do my best to cover as much packages as possible and to provide patches ASAP. Nevertheless [reporting of broken packages via issue](https://github.com/BilyakA/gentoo-clang/issues/new) and pull requests are welcome!
