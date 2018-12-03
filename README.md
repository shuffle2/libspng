# Overview

libspng is a C library for reading and writing Portable Network Graphics (PNG) 
format files with a focus on security and ease of use.

libspng is an alternative to libpng, the projects are separate and the APIs are
not compatible.

# Motivation 

The goal is to provide a PNG library with a simpler API than [libpng](https://github.com/glennrp/libpng/blob/libpng16/png.h).

Peformance is also a priority, decoding is 8% faster than libpng for truecolor images with an alpha channel and 3% faster for truecolor images.

The testsuite is designed to test both libraries, it has already uncovered a [bug](https://sourceforge.net/p/libpng/bugs/282/) in libpng.

# Versioning

Releases follow the [semantic versioning](https://semver.org/) scheme with a few exceptions:

* Releases from 0.4.0 to 0.8.x are stable
* 0.8.x will be maintained as a separate release branch from 1.0.0

API/ABI changes can be tracked [here](https://abi-laboratory.pro/index.php?view=timeline&l=libspng).

# Licensing

Code is licensed under the BSD 2-clause "Simplified" License.

The [PngSuite](http://www.schaik.com/pngsuite/) and other images from libpng are 
used for regression testing.

# Testing

libspng comes with an extensive test suite. There are over 700 test cases, 
175 [test images](http://www.schaik.com/pngsuite/) are decoded with all possible 
output format and flag combinations and compared against libpng's output. 
The testsuite also includes regression tests from libpng and is compiled with 
AddressSanitizer and UndefinedBehaviorSanitizer.

# Security

Code is written according to the rules of the 
[CERT C Coding Standard](https://wiki.sei.cmu.edu/confluence/display/c/SEI+CERT+C+Coding+Standard).
All integer arithmetic is checked for overflow and all error conditions are handled gracefully. 
Releases are scanned with Clang Static Analyzer and [Coverity Scan](https://scan.coverity.com/projects/randy408-libspng) 
and have a Defect Density of 0.00.

# Documentation

Online documentation is available at https://libspng.org/doc.

# Known Issues

* `spng_crc_set_action()` is partially implemented, `SPNG_CRC_DISCARD` has no effect.

## Building from source

Meson is the primary build system but CMake is also supported, the library only depends on zlib.

### CMake build

```
mkdir -p cbuild
cd cbuild
cmake ..
make
make install
```

### Meson build

```
meson build
cd build
ninja
ninja install
```

## Running the testsuite

Only with Meson builds, the testsuite requires libpng.

```
# Run in build directory
meson configure -Ddev_build=true
ninja test
```

## Benchmarking

```
# Run in source directory
git checkout tags/v0.4.1
wget https://gitlab.com/randy408/libspng/snippets/1786557/raw -O v041_opt.patch
git apply v041_opt.patch
git clone https://gitlab.com/randy408/benchmark_images.git
cd benchmark_images;
git checkout tags/v0.4.0
cd ../build
meson configure -Ddev_build=true -Dbuildtype=release -Db_pgo=generate
ninja benchmark
meson configure -Db_pgo=use
ninja benchmark
cat meson-logs/benchmarklog.txt
```
