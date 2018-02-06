# Gentoo-Clang overlay
An overlay for Gentoo, providing patches for upstream Gentoo repository to make it possible to build entire world with clang compiler.

## Quick start
First of all you need to build clang with clang. The easiest way is to install clang:4 and then to build clang:5 with it.

```
emerge -av1 clang:4 llvm:4
```
Now add following to your make.conf (just like [wiki](https://wiki.gentoo.org/wiki/Clang#Global_configuration_via_make.conf) says):
```
CC="clang"
CXX="clang++"
CFLAGS="-pipe"
CXXFLAGS="${CFLAGS}"
LDFLAGS="-Wl,-O3 -Wl,--as-needed"
AR="llvm-ar"
NM="llvm-nm"
RANLIB="llvm-ranlib"
```
Now you can build clang using clang (remember to remove clang:4 after you've done)
```
emerge -av clang:5 llvm:5
emerge --depclean -a
```
Before you start rebuilding entire world remember to include overrided eclasses to your gentoo repository.
Add following to `/etc/portage/repos.conf/gentoo.conf` in `[DEFAULT]` section:
```
eclass-overrides = gentoo-clang
```
Finnaly you are good to go:
```
emerge -eav1 @world
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
