# Hello World!

A simple `Hello, world!` program in Zig can be coded as:

```zig
const std = @import("std");
const writer = std.io.getStdOut().writer();

pub fn main() !void {
    writer.print("Hello, world!\n", .{});
}
```

A few things that you may notice when coming from C:
* You import libraries and modules using `@import`. 
* Printing is actually a pretty involved process. We have to grab a hold of a `writer` object from `stdout`, followed by a call to `print` which handles formatted strings.
* The 2nd argument to `print` is required, even if the string isn't formatted. In this case nothing is passed.

