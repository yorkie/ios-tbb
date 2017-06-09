# TBB for iOS and its Simulators

TBB is Intel(R) Threading Building Blocks C++ library for parallel programming. This
repository is inspired by the [@turbulenz](https://github.com/turbulenz)'s [tbb-ios](https://github.com/turbulenz/tbb-ios).

## Getting Started

First of all, clone this repository:

```sh
$ git clone git@github.com:yorkie/ios-tbb.git
```

Then start building for your iPhone or Simulator:

```sh
$ make target=ios arch=armv7 compiler=clang # for iPhone/iOS
$ make target=ios arch=x86_64 compiler=clang # for Simulator
```

Note the flag `compiler=clang` is such important, if not set, the system will use g++
as compiler, then you'd get the following report:

```
building for OSX, but linking in object file built for iOS.
```

## License

No license here, please go upstream to see what's license when you want to use.