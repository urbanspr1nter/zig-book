# Formatting

Zig supports formatted strings in output. For example, if we wanted to write decimal 256 as hex on the screen, we could print as:

```zig
try writer.print("{x}", .{256});
```

This would output something like:
```bash
100
```

Now, if we wanted to pad with zeros, we can use the `{x:0>n}` syntax where `n` is the number of digits a number is expected to have, and if unused `0` is displayed. 

```zig
try writer.print("{x:0>4}", .{256});
```

Will output:
```
0100
```

Here is a simple snippet of code where we can print several rows of 16 elements in hexadecimal. The `RAM` array has a `Capacity` of 2048 elements, and we print them in rows of 16 elements:

```zig
pub fn printMem() !void {
    var i: u32 = 0;
    while (i < Capacity) {
        if (i % 16 == 0) {
            try writer.print("\n", .{});
        }
        try writer.print("[{x:0>4}]: {d:0>3} ", .{ i, memRead(i) });
        i += 1;
    }
    try writer.print("\n", .{});
}
```

A sample row output:
```zig
[00f0]: 000 [00f1]: 000 [00f2]: 000 [00f3]: 000 [00f4]: 000 [00f5]: 000 [00f6]: 000 [00f7]: 000 [00f8]: 000 [00f9]: 000 [00fa]: 000 [00fb]: 000 [00fc]: 000 [00fd]: 000 [00fe]: 000 [00ff]: 000
```

