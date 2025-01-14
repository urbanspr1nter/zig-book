# Operators

## Increment, Decrement Operators

Unlike C, you cannot use `++` and `--`. Instead Zig is like Python where we use `+= 1` and `-= 1` to increment and decrement respectively.

## Bitwise Operators

* `&` - bitwise AND
* `|` - bitwise OR
* `>>` - right shift
* '<<` - left shift

For example, suppose we want to take elements from an array called `RAM` indexed by `CPU.ip`. The types are `u8` but we'd like to combine 4 of these to create a `u32`. We can do this: 

```zig
const result = (@as(u32, RAM[CPU.ip]) << 24 | @as(u32, RAM[CPU.ip + 1]) << 16 | @as(u32, RAM[CPU.ip + 2]) << 8 | @as(u32, RAM[CPU.ip + 3]));
```

## Casting

* To promote a smaller value to a larger value, you can use `@as`. 
* To demote a larger value to a smaller value, you can use `@truncate`. Do note that `@truncate` will infer the resulting type and also remove the most significant bits of the number.
