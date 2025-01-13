# Identifiers to Values and Variables

For value types which need an identifier to them, you can use the `const` keyword like this:

```zig
const x: i32 = 5;
```

This statement assigns `5` to `x`. It is immutable and essentially now, `x` can be referred to as an identifier to the value `5`.

For variables, the `var` keyword is used. The variable values can be updated after initial declaration, and definition.

```zig
var y = 2;
y = 4;
```

Note it is illegal to declare a variable without a definition, or assignment.

```zig
// This is bad!
var x: i32;
x = 9;

// This is good
var y: i32 = 0;
y = 9;
```

If you want to have a variable be uninitialized after definition because you may not know the value to it yet, you can assign it to `undefined`.

```zig
var x: i32 = undefined;
x = 9;
```

Basically, this is being explicit.

Be careful about coercion of the `undefined` value to another type. One that occurs, as stated in docs, you can no longer detect that a value is `undefined`. `undefined` means something in Zig -- "Do not use me. Before using, please overwrite."

## `const` vs `var`

Generally better to use `const` rather than `var`. 

## Identifiers

Here are some rules to naming identifiers:
1. They must start with an alphanumeric charactter.
2. Identifiers may only contain alphanumeric characters, or underscores. 
3. They can not be a reserved keyword.

### Any other way to have other characters?

Yes, you can declare variables using the `@""` syntax like this:

```zig
const test = @"funny#$identifier";
```
