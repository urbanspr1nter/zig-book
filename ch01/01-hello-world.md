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
* Printing is actually a pretty involved process.

