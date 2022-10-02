# Code Fork {#fork}

libE57Format is a fork of [E57RefImpl](https://sourceforge.net/projects/e57-3d-imgfmt/) v1.1.332.

The original source is from [E57RefImpl 1.1.332](https://sourceforge.net/projects/e57-3d-imgfmt/files/E57Refimpl-src/) and then everything was stripped out except the main implementation for reading and writing E57. This version also removes the dependency on [Boost](http://www.boost.org/) and requires C++11.

Many, many other changes were made prior to the first release of this fork. See the [CHANGELOG](https://github.com/asmaloney/libE57Format/CHANGELOG.md) and git history for details.

## Why Fork?

The E57RefImpl code had not been touched in years and I ([Andy Maloney](https://github.com/asmaloney/)) wanted to make changes to compile this library with macOS. Forking it gave me more freedom to update the code and make changes as required.

I changed the name of the project so that it is not confused with the **E57RefImpl** project. I have also changed the main include file's name from `E57Foundation.h` to `E57Format.h` to make sure there is no inclusion confusion.

Versions of **libE57Format** started at 2.0.
