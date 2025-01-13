# While Loops

Zig has a very C-like syntax for `while` loops. Here is an example of a basic loop where we can iterate over 23 times starting from `i = 0` to `i = 22`.

```zig
var i: u32 = 0;
while (i < 23) {
    try addYImmediate(1);
    i += 1;
}
```

