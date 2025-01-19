# Zig Build System

Zig build uses a `build.zig` file to declaratively build a graph on how to build your project.

## Unit Tests

```zig
const std = @import("std");

pub fn build(b: *std.Build) void {
    const target: std.zig.CrossTarget = b.standardTargetOptions(.{});
    const optimize: OptimizeMode = b.standardOptimizeOptions(.{});

    const exe_unit_tests = b.addTest(.{
        .root_source_file = b.path("src/main.zig"),
        .target = target,
        .optimize = optimize,
    });
    exe_unit_tests.linkLibC();
    exe_unit_tests.addIncludePath(b.path("src"));
    exe_unit_tests.addCSourceFiles(.{ .root = b.path("src"), .files = &.{"c_string.c"} });

    const run_exe_unit_tests = b.addRunArtifact(exe_unit_tests);

    const test_step = b.step("test", "Run unit tests");
    test_step.dependOn(&run_exe_unit_tests.step);
}
```

## Building a C Library

One scenario which we can use the Zig build system for is to create a C library and build it withe Zig as opposed to using tools like CMake or regular Make files.

It is actually pretty cool. Let's take a look at a basic `build.zig` file.

The following is in context to a C project with the folder structure:

```
\
    |_ src
        |_ c_string.h
        |_ c_string.c
        |_ main.zig
    |_build.zig
```

- `main.zig` is where the logic of the C library is unit tested. It will not be included in our library that we will build.
- `c_string.h` is our header which we will want to include in `include`.
- `c_string.c` is the implementation of the library. We want this compiled code to be a `.so` or `.dylib` and placed into `lib`.

```zig
const std: type = @import("std");

pub fn build(b: std.build.Builder) void {
    const target: std.zig.CrossTarget = b.standardTargetOptions(.{});
    const optimize: OptimizeMode = b.standardOptimizeOptions(.{});

    const lib = b.addSharedLibrary(.{
        .name = "cstring",
        .target = target,
        .optimize = optimize,
        .link_libc = true,
        .version = .{ .major = 1, .minor = 0, .patch = 0 }
    });

    lib.addIncludePath(b.path("src"));

    lib.addCSourceFiles(.{
        .root = b.path("src"),
        .files = &.{"c_string.c"},
    });

    b.installArtifact(lib);
    b.installFile("src/c_string.h", "include/c_string.h");
}
```

- `target` sets up which architecture, and OS to build. By default, it will use the arch and OS which the project is being built on.
- `optimize` whether to use `Debug`, `ReleaseSafe`, `ReleaseFast`, `ReleaseSmall`, etc.

We will will use `addSharedLibrary` to indicate that we want to build a shared library. A `struct` is provided with the properties necessary for this. A lot of it is self explanatory but here are some important points:

- `link_libc` tells us to link the C standard library.
- `name` is going to be the library name which C programs can link with with things like `make`, and so on. In this case since we have `cstring`, then a `make` command will have a link like this: `-lcstring`.

We will assign the result of `addSharedLibrary` to `lib` and we can then specify where our include header files exist with `addIncludePath`. Then we can individually add the C source files using `addCSourceFiles`.

Finally, we provide `lib` into the install step of `zig build install` with the `installArtifact` call. By default, `.h` header files are not included, so we manually copy it over to the `include` folder under `zig-out` with `installFile`. This is how we can ensure the expected output folder structure:

```
zig-out
    |_ lib
        |_ libcstring.so
    |_ include
        |_ cstring.h
```

### Trying it out

Build the library using:

```bash
zig build --prefix /path/to/your/install/path
```

Then run:

```bash
zig build install
```

This will then copy the files in `zig-out` to the path specified in `prefix` path.

We can create a simple C project with `main.c` and a `Makefile` and have the following definition:

```makefile
a.out:
    gcc main.c -I/path/to/prefix/include -L/path/to/prefix/lib -Wl,-rpath,/path/to/prefix/lib -lcstring
```
