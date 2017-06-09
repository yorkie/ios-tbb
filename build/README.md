# Overview

This directory contains the internal Makefile infrastructure for 
Intel® Threading Building Blocks (Intel® TBB).

See below for how to build Intel TBB and how to port Intel TBB to 
a new platform, operating system or architecture.

## To Build

To port Intel TBB to a new platform, operating system or architecture, see 
the [porting directions](#port) below.

**Software prerequisites:**

1. C++ compiler for the platform, operating system and architecture of interest. Either the native compiler for your system, or, optionally, the appropriate Intel® C++ compiler, may be used.
2. GNU make utility. On Windows OS, if a UNIX* emulator is used to run GNU make, it should be able to run Windows OS utilities and commands. On Linux OS, OS X, etc., shell commands issued by GNU make should execute in a Bourne or BASH compatible shell.

Intel TBB libraries can be built by performing the following steps. 
On systems that support only one ABI (e.g., 32-bit), these steps build 
the libraries for that ABI. On systems that support both 64-bit and 32-bit 
libraries, these steps build the 64-bit libraries (Linux OS, OS X, 
and related systems) or whichever ABI is selected in the development 
environment (Windows OS).

1. Change to the top-level directory of the installed software.
2. If using the Intel® C++ compiler, make sure the appropriate compiler is available in your PATH (e.g., by sourcing the appropriate iccvars script for the compiler to be used).
3. Invoke GNU make using no arguments, for example, 'gmake'.

To build Intel TBB libraries for other than the default ABI (e.g., to build 32-bit libraries on Linux OS, OS X, or related systems that support both 64-bit and 32-bit libraries), perform the following steps:

1. Change to the top-level directory of the installed software.
2. If using the Intel® C++ compiler, make sure the appropriate compiler is available in your PATH (e.g., by sourcing the appropriate iccvars script for the compiler to be used).
3. Invoke GNU make as follows, 'gmake arch=ia32'.

The default make target will build the release and debug versions of the Intel TBB library.

Other targets are available in the top-level Makefile. You might find the following targets useful:

- `make test` will build and run Intel TBB unit-tests;
- `make examples` will build and run Intel TBB examples;
- `make all` will do all of the above.

See also the list of other targets below.

By default, the libraries will be built in sub-directories within the build/ directory. The sub-directories are named according to the operating system, architecture, compiler and software environment used (the sub-directory names also distinguish release vs. debug libraries). On Linux OS, the software environment comprises the GCC, libc and kernel version used. On OS X, the software environment comprises the GCC and OS version used. On Windows OS, the software environment comprises the Microsoft* Visual Studio* version used. See below for how to change the default build directory.

## Port

This section provides information on how to port Intel TBB to a new platform, operating system or architecture. A subset or a superset of these steps may be required for porting to a given platform.

To port the Intel TBB source code:

1. If porting to a new architecture, create a file that describes the architecture-specific details for that architecture.
  - Create a <os>_<architecture>.h file in the include/tbb/machine directory that describes these details.
    - The <os>_<architecture>.h is named after the operating system and architecture as recognized by include/tbb/tbb_machine.h and the Makefile infrastructure.
    - This file defines the implementations of synchronization operations, and also the scheduler yield function, for the operating system and architecture.
    - Several examples of <os>_<architecture>.h files can be found in the include/tbb/machine directory.
      - A minimal implementation defines the 4-byte and 8-byte compare-and-swap operations, and the scheduler yield function. See include/tbb/machine/mac_ppc.h for an example of a minimal implementation.
      - More complex implementation examples can also be found in the include/tbb/machine directory that implement all the individual variants of synchronization operations that Intel TBB uses. Such implementations are more verbose but may achieve better performance on a given architecture.
      - In a given implementation, any synchronization operation that is not defined is implemented, by default, in terms of 4-byte or 8-byte compare-and-swap. More operations can thus be added incrementally to increase the performance of an implementation.
      - In most cases, synchronization operations are implemented as inline assembly code; examples also exist, (e.g., for Intel® Itanium® processors) that use out-of-line assembly code in *.s or *.asm files (see the assembly code sub-directories in the src/tbb directory).
  - Modify include/tbb/tbb_machine.h, if needed, to invoke the appropriate <os>_<architecture>.h file in the include/tbb/machine directory.
2. Add an implementation of DetectNumberOfWorkers() in src/tbb/tbb_misc.h, that returns the number of cores found on the system in case it is not supported by the current implementation. This is used to determine the default number of threads for the Intel TBB task scheduler.
3. Either properly define FillDynamicLinks for use in src/tbb/cache_aligned_allocator.cpp, or hardcode the allocator to be used.
4. Additional types might be required in the union defined in include/tbb/aligned_space.h to ensure proper alignment on your platform.
5. Changes may be required in include/tbb/tick_count.h for systems that do not provide gettimeofday.

**To port the Makefile infrastructure:**

Modify the appropriate files in the Makefile infrastructure to add a new platform, operating system or architecture as needed. See the Makefile infrastructure files for examples.

1. The top-level Makefile includes common.inc to determine the operating system.
  - To add a new operating system, add the appropriate test to common.inc, and create the needed <os>.inc and <os>.<compiler>.inc files (see below).
2. The <os>.inc file makes OS-specific settings for a particular operating systems.
  - For example, linux.inc makes settings specific to Linux operating systems.
  - This file performs OS-dependent tests to determine the specific platform and/or architecture, and sets other platform-dependent values.
  - Add a new <os>.inc file for each new operating system added.
3. The <os>.<compiler>.inc file makes compiler-specific settings for a particular <os> / <compiler> combination.
  - For example, linux.gcc.inc makes specific settings for using GCC on Linux OS, and linux.icc.inc makes specific settings for using the Intel® C++ compiler on Linux OS.
  - This file sets particular compiler, assembler and linker options required when using a particular <os> / <compiler> combination.
  - Add a new <os>.<compiler>.inc file for each new <os> / <compiler> combination added.




