# Simulator


## QuestaSim


### SystemVerilog


#### Local Parameter

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


#### UVM Test Class

UVM test class definition with parameters will cause its **name in factory with parameters**.
Consequently, test run can not be started due to the **bare name of the test class in factory not found**.

Use parameters only in classes below UVM test class and local parameters in UVM test class.


#### Virtual Interface Assignment

If an interface variable is declared with an undefined modport in the interface,
virtual interface assignment to this variable will be degraded to the whole interface rather than the any modport.


