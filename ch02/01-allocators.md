# Allocators

Zig doesn't have a default allocator to use when needing to allocate some memory within the heap.

In Zig, you need to specify a specific allocator if you would like to allocate some memory, or call a function which requires one to be passed. 

The easiest one to use if you're coming from the C programming language is to use the `std.heap.c_allocator` allocator. It will provide an `Allocator` instance which will contain the `malloc` and `free` equivalents.

However, in order to build your Zig program using this correctly, you'll have to link with the libc like this:

```bash
zig run main.zig -lc
```

Notice the `-lc` argument.

There are other allocators available too. For example, Zig provides a generic allocator called `std.heap.GeneralPurposeAllocator`. This can be used too. 

```zig
var genPurposeAllocator = std.heap.GeneralPurposeAllocator(.{}){};
const allocator = genPurposeAllocator.allocator();

const bytes = try allocator.alloc(u32, 5);
defer allocator.free(bytes);

bytes[1] = 56;
bytes[3] = 18;

try std.io.getStdOut().writer().print("bytes[3]: {d}\n", .{bytes[3]});
```
