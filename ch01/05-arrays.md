# Arrays

To initialize an array with a fixed capacity, for example `2048` elements, you can declare and initalize like this:

```zig
var RAM: [2048]u8 = [_]u8{0} ** 2048;
```

The `**` operator means the repeat the element a specific number of times.

Notice how the array we named `RAM` is declared as `var`. This allows us to modify the values in the array:

```zig
RAM[0] = 8;
RAM[1] = 9;
RAM[2] = 10;
```

`const` would disallow these modifications.


