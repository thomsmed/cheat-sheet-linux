# GNU Toolchain

Documentation:

- [GCC and Make](https://www3.ntu.edu.sg/home/ehchua/programming/cpp/gcc_make.html)

|Command|Description|
|---|---|
|`gcc`||
|`cpp`||
|`as`||
|`ld`||
|`make`||

## GCC (GNU Compile Collection)

```bash
# Pre-processing
cpp hello.c > hello.i
# Or
gcc -E -o hello.i hello.c

# Compilation
gcc -S hello.i

# Assemble
as -o hello.o hello.s
# Or
gcc -c hello.s

# Linking
ld -o hello hello.o ...libraries... # TODO: Complete example
# Or
gcc -o hello hello.o
```

Tip:

```bash
# use gcc with option -Wl,--verbose to get the list of libraries passed to the linker
gcc -o hello hello.c -Wl,--verbose
```

## Make (GNU Make)
