# huff-stack-generator

The goal of this project is to automatically generate the stack comments.

From this:

```huff
#define macro SET_VALUE2() = takes (2) returns (0) {
    0x04
    0x04
    _XXX_YYY
    0x04
    0x55
    lt
    0x87
    gt
}

#define macro _XXX_YYY() = takes (1) returns (1) {
    0x06
    calldataload
    [VALUE_LOCATION]
    sstore
    0x69
    0x00
}
```

To this:

```huff
#define macro SET_VALUE2() = takes (2) returns (0) {
    0x04      // [$0, $1, 0x04]
    0x04      // [$0, $1, 0x04, 0x04]
    _XXX_YYY  // [$0, $1, 0x04, _XXX_YYY: %0]
    0x04      // [$0, $1, 0x04, _XXX_YYY: %0, 0x04]
    0x55      // [$0, $1, 0x04, _XXX_YYY: %0, 0x04, 0x55]
    lt        // [$0, $1, 0x04, _XXX_YYY: %0, (0x55 < 0x04)]
    0x87      // [$0, $1, 0x04, _XXX_YYY: %0, (0x55 < 0x04), 0x87]
    gt        // [$0, $1, 0x04, _XXX_YYY: %0, (0x87 > (0x55 < 0x04))]
}

#define macro _XXX_YYY() = takes (1) returns (1) {
    0x06              // [$0, 0x06]
    calldataload      // [$0, calldataload(0x06)]
    [VALUE_LOCATION]  // [$0, calldataload(0x06), [VALUE_LOCATION]]
    sstore            // [$0]
    0x69              // [$0, 0x69]
    0x00              // [$0, 0x69, 0x00]
}
```

## How to use

```console
$ cargo run <INPUT_FILE> <OUTPUT_FILE>
```

Optional

Stack direction (default is left):

```console
stack-direction: [--(right/left)]
```

Use alt comments (default is false):

```console
use-alt: [--alt]
```

Note: this setting will add alternative comments. For example, instead of adding `call(param1, param2, ..., param7)` it will add `successBool` to the stack comments. You can change the alternative comments in the `src/opcodes.rs` file

#### Example

```console
$ cargo run /home/shafu/huff-stack-generator/macro.huff /home/shafu/huff-stack-generator/out.huff
```
