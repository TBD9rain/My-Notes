# Radiant

Radiant constraints are similar to Synopsys Design Constraint.

Timing constraints are recommended to be created with **Pre-Synthesis Design Constraint Editor**.
Physical constraints are recommended to be created with **Design Constraint Editor** and **Physical Designer**.
**Design Constraint Editor** and **Physical Designer** could navigate the port, instance, and net name after synthesis.
**Physical Designer** could create group.


## Object

Constraint design objects:
- Port: A physical FPGA package pin.
- Pin: An instance input, output or inout.
- Net: An interconnect wire.
- Clock: A port or a pin that drives sequential element in FPGA.
- Cell(Block): usually refers to an instance name or architecture primitive.

Commands to extract objects:
```
get_clocks [-regexp] [-nocase] <pattern>
get_ports [-regexp] [-nocase] <pattern>
get_pins [-hierarchical{<hierarchical>/*}] [-of_objects <objects>] [-regexp] [-nocase] <pattern>
get_cells [-hierarchical{<hierarchical>/*}] [-of_objects <objects>] [-regexp] [-nocase] <pattern>
get_nets [-hierarchical{<hierarchical>/*}] [-of_objects <objects>] [-regexp] [-nocase] <pattern>
all_clocks
all_inputs [-level_sensitive] [-edge_triggered] [-clock]
all_outputs [-level_sensitive] [-edge_triggered] [-clock]
all_registers [-no_hierachy] [-hsc_separator] [-level_sensitive] [-clock] [-rise_clock] [-fall_clock] [-cells] [-data_pins] [-clock_pins]
    [-slave_clock_pins] [-async_pins] [-output_pins] [-edge_triggered]
```

Preceding commands should be enclosed by `[]` in constraints to extract objects.

`<pattern>` could be enclosed in `{}` to include special characters.

Arguments:
- `-hierarchical{<hierarchical>/*}`:
get objects of specific subset hierarchy of the path.
- `-of_objects <objects>`:
get objects of relevant `<objects>`.


## Timing Constraint

Radiant timing analysis modeled timing paths across the entire path,
which can begin and end **outside of the FPGA boundary**.

**Pre-Synthesis Timing Constraint Editor** could be used to create timing constraints.


### Timing Constraint Methodology

1. Setting Timing Constraints
    1. Identify clocks in and create clock.
        - e.g.: `create_clock -name clk1 -period 10 [get_ports clk1]`
    2. Identify clocks of interfacing devices to FPGA and create associated virtual clocks
        - e.g.: `create_clock -name vclk1 -period 10`
    3. Identify generated clocks in and create generated clock.
        - e.g.: `create_generated_clock -name clk2 -source [get_ports clk1] -divide_by 5 [get_nets clk2]`
    4. Define input and output timing constraints.
        - e.g.: `set_input_delay -clock [get_clocks vclk1] 0.5 [get_ports din]`
        - e.g.: `set_output_delay -clock [get_clocks vclk2] 0.6 [get_ports dout]`
    5. Define clock characteristics.
        - e.g.: `set_clock_uncertainty 0.01 [get_clocks clk1]`
        - e.g.: `set_clock_latency -source 0.7 [get_clocks clk1]`
    6. Define timing exceptions, including:
        - clock groups
        - multicycle path
        - false path
        - min/max delay
2. Check Post Route Timing Report
    1. Check if timing constraints are accepted by software.
    2. Check constraint coverage.
    3. Check setup time analysis.
    4. Check hold time analysis.
3. Iterative process


### Clock

Real clock: usually a clock connecting to a port and
used internally to drive sequential elements.

Virtual clock: a clock which is available on board to clock other devices connected to FPGA.
It is not be used inside FPGA, but for **input and output delay constraints**.

Constraint syntax:
```
create_clock -period <period> [-name <clock name>] [-waveform <value1> <value2>]
    [<object>] [-add]
```

Arguments:
- `<object>`:
    including port, pins, or nets.
    **When no `<object>` argument is provided,**
    **the clock is considered as virtual.**
- `-add`:
    used to add more than one clock on the `<object>`.

Examples:
```
create_clock -name CLK1 -period 10 [get_ports CLK1]
```
Refers to a real clock in the design from port `CLK1`.

```
create_clock -name VCLK -period 10
```
In this case, `VCLK` is considered as a virtual clock.


### Generated Clock

Clocks generated internally in FPGA from a main clock port or net.
Generated clocks need to be identified in order for the implementation tool to recognize
the relationship between the main clock and generated clocks.

Constraint syntax:
```
create_generated_clock -source <src_object> [-divide_by <factor>] [-multiply_by <factor>]
    [-name <clock name>] [-add] [<object>]
```

Arguments:
- `-source <src_object>`:
the object ob which the source clock of the generated clock is defined.
- `-divide_by <factor>`:
the frequency division factor.
- `multiply_by <factor>`:
the frequency multiplication factor.

Example:
```
create_generated_clock -name clk2 -source [get_ports clk] -divide_by 5 [get_nets clk2]
```


### Clock Outside the FPGA

Due to PCB routing,
there are different latency between source clock to FPGA and other devices.


#### Clock Latency

Clock latency specifies the latency of clock outside the FPGA.

Constraint syntax:
```
set_clock_latency <value> -source [-rise | -fall] [-early | -late] <clock>
```

Arguments:
- `-rise` and `-fall`:
    indicate the delay is for the rising and falling edges of the clock.
- `-early`:
    early delay will be used as
    the capture clock delay for setup time calculation and
    the launch clock delay for hold time calculation.
- `-late`:
    late delay will be used as
    the launch clock delay for setup time calculation and
    as the capture clock delay for hold time calculation.


#### Clock Uncertainty

Clock Uncertainty indicates the clock of interest has uncertainty in its period.
Frequency variation cloud quantify this uncertainty:
$$
f_{v} = \frac {f \times ppm} {10^{-6}}
$$
$f$ represents the contral frequency,
and $ppm$ represents the peak frequency variation in percentage per million Hertz.

Constraint syntax:
```
set_clock_uncertainty <time> [-from <from clock>] [-to <to clock>]
    [-setup] [-hold] [-get_clocks <clock>]
```

### Clock Group

Clock group constraint is used to identify different clock domains.
This constraint indicates to the implementation and STA tools that specific clock domains
are not related and should be ignored for analysis.

Constraint syntax:
```
set_clock_groups [-asynchronous | -logically_exclusive | -physically_exclusive]
    -group [get_clocks <clock>] -group [get_clocks <clock>]
```

Arguments:
- `-asynchronous`:
    the clock groups are asynchronous to each other
- `-physically_exclusive`:
    the clocks are mutually exclusive.
    Only one clock group is active at any time.
- `-logically_exclusive`:
    clocks are mutually exclusive and will not reach flip-flops at the same time
    due to logic implementation.

Examples:
```
create_clock -period 10.000 -name clka [get_ports clka]
create_clock -period 10.000 -name clkb [get_ports clkb]

set_clock_groups -asynchronous -group [get_clocks clka] -group [get_clocks clkb]
# which is equivalent to:
set_false_path -from [get_clocks clka] -to [get_clocks clkb]
set_false_path -from [get_clocks clkb] -to [get_clocks clka]
```


### Input and Output Delay

The input and output delay constraint provides details about
the FPGA external timing environment.

Constraint syntax:
```
set_output_delay (-clock | -clock_fall) <clock> [-max | -min] <value> [-add_delay] <output_port>
set_input_delay (-clock | -clock_fall) <clock> [-max | -min] <value> [-add_delay] <input_port>
```

Arguments:
- `-clock | -clock_fall`:
    specify the clock reference to which the specified input delay is related.
    `clock_fall` is on the falling edge of clock.
- `-add_delay`:
    used to define more than one delay on a given port.

Examples:
```
set_input_delay 1.2 -clock [get_clocks clka] [get_ports din]
set_output_delay 1.2 -clock [get_clocks clkb] [all_outputs]
set_output_delay 1.2 -min -clock [get_clocks clkb] [get_ports dout]
set_output_delay 1.5 -max -clock [get_clocks clkb] [get_ports dout]
```


### Multicycle Path

Multicycle paths are flip flop to flip flop paths
between the same clock domain or different clock domains
that could take more than one clock cycle to transfer.

In multicycle paths,
setup time check is done $N$ clock cycles after launching edge and
hold time check is done $N-1$ clock cycles after launching edge (by default).

Constraint syntax:
```
set_multicycle_path <n_cycles> [(-from | -rise_from | -fall_from) <object>] [-through <object>]
    [(-to | -rise_to | -fall_to) <object>] [-setup | -hold]
```

Arguments:
- `-from`:
    specify the timing path start point.
- `-rise_from` and `-fall_from`:
    same as `-from` only applies to clock object.
- `-to`:
    specify the timing path end point.
- `-rise_to` and `-fall_to`:
    same as `-to` only applies to clock object.
- `-through`:
    specify the timing path through point.
- `[-setup | -hold]`:
    if no `-setup` or `-hold` is not specified,
    it is defaulted to setup.
    When a setup multicycle path is specified with $N$ cycles,
    a hold multicycle path is automatically added by tool with $N-1$ cycles.

Examples:
```
set_multicycle_path 3 -from [get_cells reg1] -to [get_cells reg2]
set_multicycle_path 4 -from [get_pins inst1/Q] -through [get_pins inst2/B] -to [get_pins inst3/D]
```


### False Path

Identify paths that are considered false and excluded from timing analysis.

Constraint syntax:
```
set_false_path [(-from | -rise_from | -fall_from) <object>] [-through <object>]
    [(-to | -rise_to | -fall_to) <object>]
```

Arguments:
- `-from`:
    specify the timing path start point.
- `-rise_from` and `-fall_from`:
    same as `-from` only applies to clock object.
- `-through`:
    specify the timing path through point.
- `-to`:
    specify the timing path end point.
- `-rise_to` and `-fall_to`:
    same as `-to` only applies to clock object.

This constraint requires one from, one to, or one through.
If any of the options are missing,
all objects in the design that satisfy the criteria of the missing option
will be used as objects of the missing option.

Example:
```
set_false_path -from [get_ports clk1] -to [get_cells reg1]
```


### Max and Min Delay

Constraint syntax:
```
set_max_delay [(-from | -rise_from | -fall_from) <object>] [-through <object>]
    [(-to | -rise_to | -fall_to) <object>] [-datapath_only] <delay>
set_min_delay [(-from | -rise_from | -fall_from) <object>] [-through <object>]
    [(-to | -rise_to | -fall_to) <object>] <delay>
```

Arguments:
- `-from`:
    specify the timing path start point.
- `-rise_from` and `-fall_from`:
    same as `-from` only applies to clock object.
- `-through`:
    specify the timing path through point.
- `-to`:
    specify the timing path end point.
- `-rise_to` and `-fall_to`:
    same as `-to` only applies to clock object.
- `-datapath_only`:
    This option calculates the Clock-to-Q delay of the first flop,
    the total delay between the flops, and the setup time of the second flop.
    This option will not consider clock skew and jitter.


## Physical Constraint

It is recommended that constraints are created in the **Physical Designer View** and **Device Constraint Editor**.
Physical constraints are stored in the **.pdc file**.


### Group

`ldc_create_group` defines a group of objects.
Only site and IO group can be created.

Constraint syntax:
```
ldc_create_group -name <group> [-bbox {height width}] <objects>
```

Arguments:
- `-bbox {height width}`:
    used to optionally define the maximum number of rows and columns.
- `<objects>`:
    either port, instance, pin, or net.
    **Object names are varied after synthesis.**
    **The modified object names could be found in the Physical Designer View.**

Example:
```
ldc_create_group -name test [get_cells {din0_crc_reg_reg[0].ff_inst din1_crc_reg_reg[0].ff_inst }]
```


### Region

`ldc_cerate_region` defines a rectangular area.

Constraint syntax:
```
ldc_cerate_region -name <region> [-site <site>] -width <width> -height <height>
```

Arguments:
- `-site <site>`:
    a slice D site as `R<n>C<m>D`, an EBR site as `EBR_CORE_R<n>C<m>`,
    or a DSP site as `MULT9_CORE_R<n>C<m>B` or `MULT18X36_CORE_R<n>C<m>`.
    Define the northwestern (upper left) corner of the region.
- `-width <width>`:
    specify the width of the region in columns.
- `-height <height>`:
    specify the height of the region in rows.

Examples:
```
ldc_create_region -name rgn0 -site EBR_CORE_R10C14 -width 3 -height 5
ldc_create_region -name rgn1 -site MULT9_CORE_R37C19B -width 3 -height 3
ldc_create_region -name rgn2 -site MULT18X36_CORE_R37C9 -width 4 -height 4
ldc_create_region -name rgn3 -site R42C9D -width 14 -height 4
```


### Locate

`ldc_set_location` could place the specified component at a specific site or bank.
Meanwhile, it could place the specified group within a specific site or region.

Constraint syntax:
```
ldc_set_location (-site <site> | -bank <bank>) <object>
ldc_set_location (-site <site> | -region <region>) <group>
```

Examples:
```
ldc_set_location -site C15 [get_ports {A}]
ldc_set_location -region rgn3 [get_group {grp3}]
```


### Prohibit

`ldc_prohibit` prohibits the use of a site or a region in PAR.

Constraint syntax:
```
ldc_prohibit (-site <site> | -region <region>)
```


### Voltage Reference

`ldc_create_vref` define a voltage reference.
The PIO site serves as the input pin for an on-chip voltage reference.

Constraint syntax:
```
ldc_create_vref -name <vref> -site <PIO_site>
```


### Voltage setting

`ldc_set_vcc` sets the voltage and/or derate for the bank or core.

Constraint syntax:
```
ldc_set_vcc (-bank <bank> | -core) (-derate <percent> | <voltage>)
```

Arguments:
- `-bank <bank>`:
    set voltage to the specified bank.
- `-core`:
    set voltage to the core.
- `-derate <percent>`:
    voltage derating percent.

Examples:
```
ldc_set_vcc -bank 1 3.3
ldc_set_vcc -bank 1 -derate -3
```


### IO Attribute

`ldc_set_port` specify the attributes of ports.

Constraint syntax:
```
ldc_set_port [-iobuf [-vref <vref>]] {<key-value list>} <ports>
```

Arguments:
- `-iobuf`:
    set IOBUF attributes.
- `-vref`:
    voltage reference.


### System Config

`ldc_set_sysconfig` is used to set system configures.

Constraint syntax:
```
ldc_set_sysconfig {<key-value list>}
```


## Constraint Priority


### Timing Constraints

- Timing constraints that have a higher priority than others take precedence:
    1. `set_clock_groups`
    2. `set_false_path`
    3. `set_max_delay` and `set_min_delay`
    4. `set_multicycle_path`
    5. `create_clock`
- When more than one constraint of the same type are applied,
    the more specific preferences take precedence.
- When more than one constraint with the same type and specificity are applied,
    the constraint that occurs last in the constraint file takes precedence.


### Physical Constraints

- When more than one constraint of the same type are applied,
    the more specific preferences take precedence.
- When more than one constraint with the same type and specificity are applied,
    the constraint that occurs last in the constraint file takes precedence.


