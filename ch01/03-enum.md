# Enums

You can declare and use enum values like this:

```zig
const OpCode = enum {
    AddX,
    AddXImmediate,
    AddY,
    AddYImmediate,
    SubX,
    SubXImmediate,
    SubY,
    SubYImmediate
};
```

You can then use your `enum` like this:

```zig
pub fn runOp(opcode: OpCode) !void {
    switch (opcode) {
        OpCode.AddX => {
            try addX();
        },
        OpCode.AddXImmediate => {
            try addXImmediate(1);
        },
        OpCode.AddY => {
            try addY();
        },
        OpCode.AddYImmediate => {
            try addYImmediate(1);
        },
        OpCode.SubX => {
            try subX();
        },
        OpCode.SubY => {
            try subY();
        },
        OpCode.SubXImmediate => {
            try subXImmediate(1);
        },
        OpCode.SubYImmediate => {
            try subYImmediate(1);
        },
    }
}
```
