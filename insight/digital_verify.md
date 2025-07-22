# Simulator


## QuestaSim


### SystemVerilog

`localparam` definitions are not allowed in the parameter port in a class definition.

For example, the following codes cause an error report in QuestaSim:
```systemverilog
class TestEnv #(
    parameter DATA_WIDTH = 8,

    localparam DATA_IN_WIDTH = DATA_WIDTH,
    localparam DATA_OUT_WIDTH = 2*(DATA_IN_WIDTH + 1));

    //  other codes

endclass
```
They should be replaced with:
```systemverilog
class TestEnv #(
    parameter DATA_WIDTH = 8,

    parameter DATA_IN_WIDTH = DATA_WIDTH,
    parameter DATA_OUT_WIDTH = 2*(DATA_IN_WIDTH + 1));

    //  other codes

endclass
```
or
```systemverilog
class TestEnv #(
    parameter DATA_WIDTH = 8);

    localparam DATA_IN_WIDTH = DATA_WIDTH;
    localparam DATA_OUT_WIDTH = 2*(DATA_IN_WIDTH + 1);

    //  other codes

endclass
```

