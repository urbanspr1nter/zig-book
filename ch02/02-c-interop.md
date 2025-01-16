# C Interop

Interoperability in C is possible in Zig. At first, it seems and feels a little awkward, but it is not that bad once basics are understood. You can read the Zig documentation for that. For now, this document is just to walk through a basic example on how we can enable a Zig program to interoperate with C code.

We'll have a simple C library that just creates/duplicates a given `const char*` string and a custom `struct` to represent a string. These 2 files are: `c_string.h` and `c_string.c`.

In `c_string.h`, our `struct String` looks like this:
```c
extern struct String {
    char* text;
    uint32_t length;   
};

typedef struct String String;
```

We'll come back to why `extern` later.

We'll also declare a simple function to "create" a `String`:
```c
String* cstring_create(String* result, const char* s); 
```

Notice that this function requires a pointer to be supplied to store a result of the duplicated string, `s`. This will be returned by the `cstring_create` function.

So our entire file looks like this:
```c
#ifndef LIB_C_STRING_H
#define LIB_C_STRING_H

#include <stdint.h>

extern struct String {
    char* text;
    uint32_t length;
};

typedef struct String String;

String* cstring_create(String* result, const char* s);

#endif

```

What about `extern`? Why do we need to do that? It is so that on the Zig side of things, this `struct String` can be exposed, and therefore accessible for interoperability.

Now we can implement `c_string.c`:

```c
#include "c_string.h"

String* cstring_create(String* result, const char* s) {
	uint32_t len = strlen(s);

	result = malloc(sizeof(String));	
	result->text = strdup(s);
	result->length = len;

	return result;	
}
```

At this point, we're done implementing our C library. Now, how do we interoperate with Zig?

## Interop with C in Zig

The process to interop with C is not just only in code, but knowledge in that we must:
* Link with `libc`
* Include the necessary `.c` files to be compiled (`c_string.c`)

We can build our project in the command line with this information, or we can modify the `build.zig` project to do that. We'll go over both.

Let's create the Zig project with `zig init` in a way where `main.zig` and `c_string.h` will live in the same directory.

## main.zig

We can make use of `@cImport` builtin in Zig to get this done.

```zig
const c_string = @cImport({
    @cInclude("c_string.h");
});
```

This will try to look for the `c_string.h` file in all include directories. By default it will be the global includes. You'll have to do a bit of extra work to make it look for headers in some other directory. In this case we want the `zig` compiler to look at the current `src` directory of the project. We'll get there. For now, let's just write the code to actually call our `cstring_create` function in Zig:

```zig
pub fn main() !void {
    var buffer: [*c]c_string.String = undefined;

    buffer = c_string.cstring_create(buffer, "hello world");

    try std.io.getStdOut().writer().print("Buffer is: {s}, Length is: {d}\n", .{ buffer.*.text, buffer.*.length });
}
```
Lots of things going on above here. 

First, we leverage the `extern` access modifier of our `struct String` and access it from Zig directory by simply using the imported `c_string` module. We create a pointer `buffer` of this type. Since this is an uninitialized pointer, we assign `buffer` to `undefined`.

Next, we just call our C function and reassign it to `buffer`.

Finally, we print out the information.

## Building

We can run the program from the command line like this:
```bash
zig run main.zig c_string.c -lc -I. 
```
Let's digest what this command means:
* We tell `zig` to compile the 2 files: `main.zig` and `c_string.c` and run them when done.
* We must link with `libc` because we are wanting to also build C code... this is with `-lc`.
* We also need to specify additional include directories for `zig` to search for any additional headers. In this case, our current directory `.` -- `-I.`.

After doing that we get:

```sh
Buffer is: hello world, Length is: 11
```

# build.zig

We can modify the `build.zig` file to do the same:

```zig
const exe = b.addExecutable(.{
    .name = "libcstring",
    .root_source_file = b.path("src/main.zig"),
    .target = target,
    .optimize = optimize,
});

// Everything here is interesting:
exe.linkLibC();
exe.addIncludePath(b.path("src"));
exe.addCSourceFiles(.{ .root = b.path("src"), .files = &.{"c_string.c"} });
```

You can see that the extra 3 lines are just declaritively translating the same command line args.
