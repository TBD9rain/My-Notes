# Diamond

In Diamond, preferences are used to constrain designs in FPGA rather than constraints.

Timing and physical preferences are recommended to be created with **Spreadsheet View**.
**Netlist View** could navigate the port, instance, and net name after synthesis.


## Object

For timing preference:
- PORT: a top level signal port.
- PIN: an internal module signal port.
- NET: a wire connection.
- CLKPORT: a clock port.
- CLKNET: a clock net.
- COMP: a name of a logical component after mapping.
- ASIC: a black box or elements in the "ASIC component" section of MAP report.
- CELL: an element not in the "ASIC component" section of MAP report.
- GROUP: a user defined group.

For physical preference:
- BLKNAME: a name of a logical block before mapping.
- COMP: a name of a logical component after mapping.
- SITE: a position of real FPGA component on silicon.
- BANK: an IO bank.
- GROUP: a user defined group.
- UGROUP: a universal logical group.
- HGROUP: a hierarchical logical group.
- REGION: a rectangular area on FPGA silicon.

For system preference:
- ALLPORTS: all top level ports.
- ALLNETS: all wire connections.
- RESETPATHS: all asynchronous set/reset pahts.
- ASYNCPATHS: all paths from pads to FF inputs for the frequency and period preferences.
- JTAGPATHS: all output signals from "JTCK" of cellmode "JTAG".

Object names should be enclosed by `""`.

**Use the "Netlist View" to find a object name after synthesis.**


### Define Group

`DEFINE GROUP` defines a single identifier that will refer to a group of objects for timing preferences.

Preference syntax:
```
DEFINE (PORT | CELL | ASIC) GROUP <group_name>
    {(ASIC <identifier> PIN <identifier>) | <object>};
```

Examples:
```
DEFINE CELL GROUP "slow_group" "data*" "other_reg_{0-1}""flop";
DEFINE PORT GROUP "fast_ins" data* adr*;
DEFINE ASIC GROUP "asic1group" ASIC "serdes_fifo0_3" PIN "Q34" ASIC "serdes_fifo0_3" PIN "Q33";
DEFINE ASIC GROUP "asic2group" ASIC "serdes_fifo3_0" PIN "D1*" ASIC "serdes_fifo3_0" PIN "D2*";
```


### Universal Group

Universal group is used to infer a single placement group for multiple **instances** from the logical domain.

Preference syntax:
```
UGROUP <ugroup> [BBOX <height> <weight>]
    {BLKNAME <block>};
```

Arguments:
- `BBOX <height> <weight>`:
    define the maximum number of rows and columns of the group's bounding box.
- `BLKNAME <block>`:
    name of the logical block in the EDIF/NGD netlist ("Netlist" view in Diamond).
    A given block can only be assigned membership in one group.

Example:
```
UGROUP "rot_grp" BBOX 8 30
    BLKNAME reg_0
    BLKNAME reg_1;
```


### Hierarchical Group

Hierarchical group is used to infer a unique placement group for each instance of a **module** from the logical domain.
It is commonly defined as part of a VHDL architecture or a Verilog module declaration.
Hierarchical group must be created as an HDL attribute in source codes or
a logical preference in .lpf file with text editor.
Diamond preference-editing tools are not able to create hierarchical

Preference syntax:
```
HGROUP <hgroup> TYPE <type> [BBOX <height> <width>]
```

Arguments:
- `TYPE <type>`:
    a Verilog module or VHDL architecture name after synthesis in Netlist view.
- `BBOX <height> <weight>`:
    define the maximum number of rows and columns of the group's bounding box.

Example:
```
HGROUP "h1" TYPE "ANEB2";
```

Where "ANEB2" is a module name in Verilog or an architecture name in VHDL.


### Define Region

Region is a rectangular area defined by user that will be used to place universal groups.

Preference syntax:
```
REGION <region> <site> <height> <width> [DEVSIZE | PFUSIZE];
```

Arguments:
- `<site>`:
    a slice D site as `R<n>C<m>D`, an EBR site as `EBR_R<n>C<m>`, or a DSP site as `DSP_R<n>C<m>`.
    Define the northwestern (upper left) corner of the region.
- `<height>`:
    the height of the region in rows.
- `<width>`:
    the width of the region in columns.
- `[DEVSIZE | PFUSIZE]`:
    `PFUSIZE` indicates that all rows are PFU rows.
    `DEVSIZE` is the default value, indicating all rows including non-PFU rows.

Example:
```
REGION "reigon_0" "R16C2D" 11 30 DEVSIZE;
```


## Timing Preference (Constraint)

Diamond timing analysis modeled timing paths **within the FPGA boundary**.


### Frequency

`FREQUENCY` identifies the **minimum** operating frequency
for all sequential output to sequential input pins clocked by the specified net or port.

Preference syntax:
```
FREQUENCY <freq_val> [<freq_unit>]
    [HOLD_MARGIN <time_val> [<time_unit>]] [CLOCK_JITTER <time_val> [<time_unit>]];
FREQUENCY NET <net_name> <freq_val> [<freq_unit>]
    [PAR_ADJ <par_adj_val>] [HOLD_MARGIN <time_val> [<time_unit>]];
FREQUENCY PORT <port_name> <freq_val> [<freq_unit>]
    [PAR_ADJ <par_adj_val>] [HOLD_MARGIN <time_val> [<time_unit>]] [CLOCK_JITTER <time_val> [<time_unit>]];
```

Arguments:
- `<freq_unit>`:
    MHz | kHz, defaults to MHz.
- `<time_unit>`:
    ms | us | ns | ps | fs, defaults to ns.
- `HOLD_MARJIN <time_val> <time_unit>`:
    a hold margin is extra time slot added to the hold time for tighten constraint.
- `CLOCK_JITTER <time_val> <time_unit>`:
    a peak-to-peak jitter value for the top level input clock.
    It's only available for clock **port**.
- `PAR_ADJ <par_adj_val>`:
    add an absolute value used to adjust the frequency.
    The `<par_adj_val>` should be a positive float value.
    It allows to tighten requirements for placement and routing
    while preserving the requirements reported by static timing analysis tool.

If no net or port name is given,
the preference applies **to all clock nets** in the design that do not have a specific clock frequency preference.

Examples:
```
FREQUENCY NET "clk1" 100 MHz;
FREQUENCY 20 MHz;
FREQUENCY NET "clk2" 250.0 MHz PAR_ADJ 25;
FREQUENCY NET "clk3" 100 MHz HOLD_MARGIN 1 ns;
FREQUENCY PORT "clk4" 100 MHz CLOCK_JITTER 1 ns;
```


### Period

`PERIOD` works the same way as `FREQUENCY`,
except that `PERIOD` is given in time.

Preference syntax:
```
PERIOD <time_val> [<time_unit>]
    [(LOW | HIGH) <time_val> [<time_unit>]] [(LOW | HIGH) <time_val> [<time_unit>]]
    [HOLD_MARGIN <time_val> [<time_unit>]] [CLOCK_JITTER <time_val> [<time_unit>]];
PERIOD NET <net_name> <time_val> [<time_unit>]
    [(LOW | HIGH) <time_val> [<time_unit>]] [(LOW | HIGH) <time_val> [<time_unit>]]
    [PAR_ADJ <par_adj_val>] [HOLD_MARGIN <time_val> [<time_unit>]];
PERIOD PORT <port_name> <time_val> [<time_unit>]
    [(LOW | HIGH) <time_val> [<time_unit>]] [(LOW | HIGH) <time_val> [<time_unit>]]
    [PAR_ADJ <par_adj_val>] [HOLD_MARGIN <time_val> [<time_unit>]] [CLOCK_JITTER <time_val> [<time_unit>]];
```

Arguments:
- `<time_unit>`:
    ms | us | ns | ps | fs, defaults to ns.
- `(LOW | HIGH) <time_val> [time_unit]`:
    specify clock pulse polarity and width.
- `HOLD_MARJIN <time_val> <time_unit>`:
    a hold margin is extra time slot added to the hold time for tighten constraint.
- `CLOCK_JITTER <time_val> <time_unit>`:
    a peak-to-peak jitter value for the top level input clock.
    It's only available for clock **port**.
- `PAR_ADJ <par_adj_val>`:
    add an absolute value used to adjust the frequency.
    The `<par_adj_val>` should be a positive float value.
    It allows to tighten requirements for placement and routing
    while preserving the requirements reported by static timing analysis tool.

If no net or port name is given,
the preference applies **to all clock nets** in the design that do not have a specific clock frequency preference.

Examples:
```
PERIOD NET "clk1" 100 NS HIGH 75 NS;
PERIOD PORT "clk2" 30 NS;
PERIOD NET "clk3" 5 ns PAR_ADJ 0.5 ;
PERIOD NET "clk4" 10.000 ns HOLD_MARGIN 1 ns;
PERIOD PORT "clk5" 30 ns CLOCK_JITTER 1 ns ;
```

The following preferences are identical in PAR but different in timing report.
```
PERIOD NET  "CLK3" 100 ns  HIGH 60 ns  PAR_ADJ 10;
PERIOD NET  "CLK3" 90 ns  HIGH 54 ns;
```


### Clock Skew

`CLKSKEWDIFF` adds skew between two top level input clocks.

Preference syntax:
```
CLKSKEWDIFF CLKPORT <clk_port0> CLKPORT <clk_port1>
    <max_skew_time> [<time_unit>] [MIN <min_skew_time> [<time_unit>]];
```

Arguments:
- `<time_unit>`:
    ms | us | ns | ps | fs, defaults to ns.
- `<max_skew_time> [<time_unit>]`:
    specify the maximum skew between two clocks.
- `MIN <min_skew_time> [<time_unit>]`:
    specify the minimum skew between two clocks.

`CLKSKEWDIFF` specifies that the `<clk_port0>` arrives at the clock input **later than** behind `<clk_port1>`.

Examples:
```
CLKSKEWDIFF CLKPORT "clock1" CLKPORT "clock2" 2 NS;
```

The following preferences are identical, and both imply the other.
```
CLKSKEWDIFF CLKPORT "clka" CLKPORT "clkb" 1.4 NS MIN 0.4 NS;
CLKSKEWDIFF CLKPORT "clkb" CLKPORT "clka" -0.4 NS MIN -1.4 NS;
```


### Input and Output


#### Input Setup and Delay

Input setup is the time between
when the data arrives at its FPGA input pin and
when the next clock edge arrives as its FPGA pin.
Input setup is a positive value if the data arrives before this clock edge.

Input delay is the time between
when the previous capture clock edge arrived at FPGA pin and
when the data arrives at FPGA pin.

Input setup and input delay are related:
$$
clock\ period = input\ setup + input\ delay
$$

`INPUT_SETUP` specifies a setup or a delay time requirement
for input ports relative to a clock net.

Preference syntax:
```
INPUT_SETUP (PORT <port_name> | GROUP <group_name> | ALLPORTS)
    (<time_val> [<time_unit>] | INPUT_DELAY <time_val> [<time_unit>])
    [HOLD <time_val> [<time_unit>]]
    (CLKPORT[=]<clock_port> | CLKNET[=]<clock_net>)
    [PLL_PHASE_BACK] [CLK_OFFSET <factor> X] [SS];
```


Specify the minimum arrival time difference for setup analysis:
$$t_{clock\_arrive} - t_{data\_arrive}$$
defaults to a clock cycle which leads to **setup time under-constrained without this preference**.

Arguments:
- `<time_unit>`:
    ms | us | ns | ps | fs, defaults to ns.
- `INPUT_DELAY`:
    specify input delay instead of
    the minimum arrival time difference for setup analysis.
- `[HOLD <time_val> [<time_unit>]]`:
    specify the maximum arrival time difference for hold analysis:
    $$t_{data\_arrive} - t_{clock\_arrive}$$
    defaults to 0 which leads to **hold time over-constrained without this argument**.
    If `CLK_OFFSE <factor> X` is defined,
    the clock offset should be added into this `<time_val>`.
- `PLL_PHASE_BACK`:
    reverse the offset direction of phase delay programmed into a PLL.
- `CLK_OFFSET <factor> X`:
    adjust the analysis by a multiple of the clock period.
    `<factor>` cloud be a signed floating number.
    By default, `<factor>` is 0.
    This argument could be applied for multicycle path and opposite clock edge.
- `SS`:
    indicate that the data input and clock input are source-synchronous.
    This allows input jitter specified on the clock to be ignored
    for the input port setup and hold timing analysis.

The printed message "hold offset \<data\> to \<clk\>" in hold analysis of Diamond 3.11.1.441
is not correct.
It should be:
$$t_{hold\_time}$$
while it is actually:
$$t_{hold\_time}+t_{clk\_offset}$$

However, the hold time analysis is correct.

Examples:

The following preferences are identical when clk is period is 10 ns.
```
PERIOD PORT "clk" 10 NS;
INPUT_SETUP "in_data1" INPUT_DELAY 6 CLKPORT "clk";
INPUT_SETUP "in_data1" 4 CLKPORT "clk";
```

The following preference add an additional half period to the clock to out path,
which could be used for a rise edge launching and a following fall edge capture.
```
INPUT_SETUP PORT “RESP_REQ” 4 NS CLKPORT = "clk" CLK_OFFSET 0.5X;
```

The following preference subtract an half period to the clock to out path,
which could be used for a rise edge launching and a preceding fall edge capture.
```
INPUT_SETUP PORT “RESP_REQ” 4 NS CLKPORT = "clk" CLK_OFFSET -0.5X;
```


#### Clock to Output and Output Delay

Preference syntax:
```
CLOCK_TO_OUT (PORT <port> | GROUP <group> | ALLPORTS)
    ([MAX] <time_val> [<time_unit>] | OUTPUT_DELAY <time_val> [<time_unit>])
    [MIN <time_val> [<time_unit>]]
    (CLKPORT[=]<clock_port> | CLKNET[=]<clock_net>)
    [FROM <startpoint>]
    [CLKOUT (PORT | PIN)[=]<out_clock>]
    [PLL_PHASE_BACK] [CLK_OFFSET <clock_num> X];
```

Arguments:
- `<time_unit>`:
    ms | us | ns | ps | fs, defaults to ns.
- `[MAX] <time_val> [<time_unit>]`:
    specify the maximum time from clock to FPGA output pin
    for setup analysis.
- `[MIN] <time_val> [<time_unit>]`:
    specify the minimum time from clock to FPGA output pin
    for hold analysis.
- `(CLKPORT[=]<clock_port> | CLKNET[=]<clock_net>)`:
    `CLKPORT` starts from FPGA physical pin.
    `CLKNET` starts from FPGA internal resource output.
- `OUTPUT_DELAY`:
    specify output delay instead of clock to output.
- `FROM <startpoint>`:
    specify the clock to output path start point.
- `CLKOUT (PORT | PIN)[=]<out_clock>`:
    the name of the output clock port with respect to the output data delay
    that needs to be to be measured.
    This is used for source synchronous interfaces.
- `PLL_PHASE_BACK`:
    reverse the offset direction of phase delay programmed into a PLL.
- `CLK_OFFSET <clock_num> X`:
    adjust the analysis by a multiple of the clock period.
    `<clock_num>` cloud be a signed floating number.

Example:
```
CLOCK_TO_OUT "dout" MAX 10 NS MIN 4 NS CLKPORT = "clkin" CLKOUT PORT "clkout";
```

The preceding preference constrains that
"dout" is an output data port clocked by a signal from port "clkin".
And the minimum allowable output delay on "dout" with respect to output port "clkout" is 4 ns.


### Multicycle path

`MULTICYCLE` allows for timing relaxation of previously
defined `PERIOD` or `FREQUENCY` preferences on a path.

Preference syntax:
```
MULTICYCLE [FROM <path_elem> [CLKNET <src_clk>]]
    [TO <path_elem> [CLKNET <des_clk>]]
    [SAMECLKEN | CLKEN_NET <net>]
    (<factor> (X | X_SOURCE | X_DEST) | <time_val> NS)
```

Arguments:
- `<time_unit>`:
    ms | us | ns | ps | fs, defaults to ns.
- `<path_elem>`:
    `(PORT | CELL | GROUP | (ASIC <asic> PIN <pin>)) <identifier>`.
- `SAMECLKEN`:
    the multicycle path are defined at the source/destination register
    controlled by the same clock-enable signal.
- `CLKEN_NET <net>`:
    the multicycle paths are defined at the source/destination register
    controlled by a given `<net>`.
- `<factor> (X | X_SOURCE | X_DEST)`.ncd:
    `<factor>` is a multiple of the clock period for the clock driving the components.
    The timing constraint is shifted by $factor-1$ times the source or destination clock period.
    If no `MULTICYCLE` is applied, it equals to `MULTICYCLE` with `1 X`.
    Destination clock period will be applied to the factor when `X` or `X_DEST` is used.
    Source clock period will be applied when `X_SOURCE` is used.
- `<time_val> NS`:
    delay for the path in nanoseconds.

If the source clock and destination clock are different,
they must be related (e.g., from the same PLL, constrained with `CLKSKEWDIFF`).

The multiple can be set no greater than 0.
This ability to specify less than or equal to zero multicycles
should only be used to block paths from timing analysis
that are known to have desirable timing.

A 0x will use 0 clock cycles for setup,
which simply calculates clock skew.

Examples:
```
MULTICYCLE FROM CELL "R_REG1" TO CELL "R_REG3" 2 X;
MULTICYCLE TO GROUP "asic2group" 10 ns;
```


### Max Delay

`MAXDELAY` specifies a maximum total delay for a net, bus, or the path in the design.
When a net or bus is specified,
the maximum delay constraint applies to all driver-to-load connections
that belong to the net or bus.
When a path is specified,
the delay is the constraint for the path including net and component.

Preference syntax:
```
MAXDELAY FROM <path_elem> {TO <path_elem>} <time_val> [<time_unit>]
    [DATAPATH_ONLY] [MIN <time_val> [<time_unit>]];
MAXDELAY (NET <net> | BUS <bus> | ALLPATHS | ALLNETS) <time_val> [<time_unit>];
```

Arguments:
- `<time_unit>`:
    ms | us | ns | ps | fs, defaults to ns.
- `<path_elem>`:
    `(PORT | CELL | GROUP | (ASIC <asic> PIN <pin>)) <identifier>`.
- `DATAPATH_ONLY`:
    use to only include data path delay and
    clock skew, setup, hold requirement will be ignored.
- `[MIN <time_val> [<time_unit>]]`:
    also specify minimum path delay.
    `MIN` is not supported for `NET`, `BUS`, `ALLNETS`, and `ALLPATHS`.

Examples:
```
MAXDELAY FROM CELL "BLKWR_SM_1/stat_reg4" TO CELL "R5_MUX_1/RAM_OE_N_reg" 15 NS;
MAXDELAY FROM PORT "a1" to PORT "d1" 5 ns MIN 2 ns;
```


### Block

`BLOCK` blocks timing analysis on nets, paths, buses,
or component pins that are irrelevant to the timing of the design.
For different object:
- net:
    the net and all paths through the net will be blocked.
- bus:
    all nets in the bus, and all paths through the nets will be blocked.
- component pin:
    all paths through the pin of the component will be blocked.

The TRACE report will include all paths that are covered with `BLOCK PATH`,
but it will not include details for `BLOCK NET`.
The signal specified with `BLOCK NET` is removed from timing-graph analysis completely.
Because there is no path or coverage related to this signal any longer,
all paths through the signal are blocked/removed and considered covered.

Preference syntax:
```
BLOCK NET <net>;
BLOCK BUS <bus>;
BLOCK COMP <comp> PIN <pin>;
BLOCK PATH FROM <path_elem> TO <path_elem>
BLOCK (RESETPATHS | ASYNCPATHS | JTAGPATHS | RD_DURING_WR_PATHS | INTERCLOCKDOMAIN PATHS)
```

Arguments:
- `<path_elem>`:
    `(CELL <cell> | PORT <port> | CLKNET <clk> | ASIC <asic> PIN <pin> | GROUP group)`.
    The `FROM <path_elem>` and `TO <path_elem>` must be from the same category:
    1. `CELL`, `PORT`, `ASIC PIN`, `GROUP`
    2. `CLKNET`
- `RESETPATHS`:
    block all asynchronous set/reset paths,
    through an asynchronous set/reset pin on a design component.
    This is default blocked in the default .lpf file when a project is created.
- `ASYNCPATHS`:
    block all paths from pads to FF inputs for the frequency and period preferences.
    This is default blocked in the default .lpf file when a project is created.
- `JTAGPATHS`:
    identify all output signal from "JTCK" of cellmode "JTAG" and
    block all registers driven by these signals when analyzing maximum frequency.
- `RD_DURING_WR_PATHS`:
    block RAM read paths during the write operation.
- `INTERCLOCKDOMAIN PATHS`:
    block all paths involving data transfer between registers that are clocked by different clock nets,
    even the clock nets are related.
- `JITTER`:
    block jitter from timing analysis,
    including clock jitter specified by `FREQUENCY`, `PERIOD`, and system jitter.

For CDC path blocking,
`FROM CLKNET <clk1> TO CLKNET <clk2>` is recommended rather than `INTERCLOCKDOMAIN PATHS`.

Examples:
```
BLOCK NET "n1";
BLOCK PATH FROM CLKNET "clk1" TO CLKNET "clk2";
```


## Physical Preference (Constraint)

Physical preferences are strongly correlated with specific target devices.

Meanwhile it is recommended to create physical preferences with Spreadsheet view in Diamond
which contains information of target devices.


### Locate

`LOCATE` locks a specified component to a specified site or bank.
When a `UGROUP` object is given,
`LOCATE` anchors the upper left corner to the specified site or within the specified region.

Preference syntax:
```
LOCATE COMP <comp> [SITE <site> | BANK <bank>];
LOCATE UGROUP <ugroup> [SITE <site> | REGION <region>];
```

In `LOCATE UGROUP <ugroup> SITE <site>` `SITE` cloud be a slice site or an EBR site.
If `SITE <site>` is used to lock a slice site,
the slice name must end with the letter "D".

Examples:
```
LOCATE COMP "data1" SITE "B1";
LOCATE COMP "data2" BANK 0;
LOCATE COMP "inst1/PLLInst_0" SITE "PLL_R43C5";
LOCATE UGROUP "ugroup_0" SITE "R2C2D";

REGION "my_region" "R3C3D" 17 9;
LOCATE UGROUP "ugroup_1" REGION "my_region";
```


### IO Port


#### IO Port Attribute

Diamond specify IO port attributes with `IOBUF` preference.
`IOBUF` preferences are the only constraints that are not written to the physical preference file (.prf),
but the only constraints are written to the physical design file (.ncd).

Preference syntax:
```
IOBUF [PORT <port> | groupt <group> | ALLPORTS] {<keyword>=<value>};
```

Argument:
- `<keyword>`:
    IO\_TYPE, DRIVE, PULLMODE, SLEWRATE...


#### Voltage Reference Input Pin Assignment

Assigns a PIO site that will serve as the input pin for an on-chip voltage reference.
Referenced input voltage pins are required when implementing an externally referenced signaling standard such as HSTL or SSTL.

Preference syntax:
```
LOCATE VREF <vref_name> SITE <site> IOTYPE <iotype>;
```

Argument:
- `IOTYPE <iotype>`:
    possible `<iotype>` includes
    `HSTL18_I | HTSL18_II | HSTL15_I | SSTL33_I | SSTL3D_II | SSTL25_I, SSTAL25_II, SSTL18_I | SSTL18_II`.
    Detailed IO type should follow the target device datasheet.

#### IO Register

Specify the given register to be used as an IO register.

Preference syntax:
```
USE (DIN | DOUT) [TRUE | FALSE] CELL <cell>;
```

Arguments:
- `DIN | DOUT`:
    either input or output.
- `TRUE | FALSE`:
    The default value is TRUE.
    TRUE means that registers will be moved into IOs,
    and FALSE means that registers will be moved out of IOs.
- `CELL <cell>`:
    an IO register.


### Clock Routing resource

Primary clock resource is available for clock nets routing in PAR.
Secondary clock resource is available for clock nets, clock enable nets, or local set/reset nets routing in PAR.
Diamond could specify to use primary and secondary clock resource with preferences.

Preference syntax:
```
USE PRIMARY [PURE | DCS] NET <net> [{<quadrant>}];
USE SECONDARY NET <net> [{quadrant}];
```

Arguments:
- `PURE | DCS`:
    without this argument,
    PAR routes clock spine with DCS or PURE resource.
    `PURE` specifies PAR to route clock spine without DCS routing resource.
    `DCS` specifies PAR to route clock spine only with DCS routing resource.
- `<quadrant>`:
    specify the clock routing quadrant with combination of `QUADRANT_TL | QUADRANT_TR | QUADRANT_BL | QUADRANT_BR`.
    A clock cannot be distributed to a set of quadrants where two of the quadrants are diagonal from each other
    unless the clock is distributed to all quadrants.


### Prohibit

Diamond could prohibit the use of specified resource in FPGA during PAR.

Preference syntax:
```
PROHIBIT SITE <site>;
PROHIBIT REGION <region>;
PROHIBIT PRIMARY NET <net>;
PROHIBIT SECONDARY NET <net>;
```

Arguments:
- `SITE <site>`:
    site of resource in FPGA.
- `REGION <region>`:
    region defined previously.
- `PRIMARY NET <net>`:
    primary clock resources for routing the net.
- `SECONDARY NET <net>`:
    secondary clock resources for routing the nest.


## Other Preference


### User code

Diamond could specify a 32-bit code for storing user defined information as firmware version number,
manufacturer’s ID, or etc.

In addition to the physical preference file (.prf),
user code is recorded in the data file:
- JEDEC file (.jed) for non-volatile devices
- bitstream file (.bit) for volatile devices
The user code can be read back by means of the JTAG port using the Diamond Programmer, a third-party programmer, or an embedded system.

Preference syntax:
```
USERCODE [BIN | HEX | ASCII | AUTO] <usercode>
```

Arguments:
- `AUTO`:
    available only for ECP5 devices.


## Preference Precedence Rule


### Timing Priority

- When different timing preferences are applied,
    the preferences having higher priority take precedence:
    1. `BLOCK`
    2. `INPUT_SETUP` and `CLOCK_TO_OUT`
    3. `MULTICYCLE`
    4. `MAXDELAY`
    5. `FREQUENCY` and `PERIOD`
- When different timing preferences with the same priority are applied,
    the preferences which are more specific take precedence.
- When more than one timing preferences with the same priority and the same specificity level are applied,
    the preferences that occur last in the preference file take precedence.


### Physical Preference

1. Preferences defined in an LPF take precedence over attributes and directives defined in the HDL codes.
2. When `IOBUF` attributes exist in the LPF file and in the HDL for the same port or port group,
    those in the LPF file will completely override those in the HDL.
    Two exceptions to this rule are `IO_TYPE` and placement preference `LOC`.
3. All `IOBUF` attributes that exist in the HDL will get overridden by the MAP process when
    the `ALLPORTS` attribute is used in the LPF file.
4. Preferences that are more specific take precedence over less specific ones.
5. If preferences are at the same level and are in conflict,
    preferences defined later in the LPF take precedence over those defined earlier


