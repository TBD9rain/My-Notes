# Introduction

SystemVerilog and Verilog are both hardware description languages and executed parallel in simulators.
The execution of certain language constructs is defined by parallel execution of blocks or processes.
It is important to understand what execution order is guaranteed to the user and what execution order is indeterminate.

Terms in an event execution model:

- *Process*, concurrently scheduled elements, like modules, initial and always procedures,
continuous assignments, procedural assignments, asynchronous tasks...
- *Update event*: change in state of a net or variable in the system description begin simulated.
- *Evaluation event*: evaluation of a process, a event as well.
- *Simulation time*: the time value maintained by the simulator to model the actual time it would take for the system description being simulated.


# Verilog


## Stratified Event Queue

Verilog event queue consists of 5 regions:

1. ***Active events***,
occur at the current simulation time and can be processed in any order (causes nondeterminism).
Processing of all the active events is called a *simulation cycle*.

2. ***Inactive events***,
occur at the current simulation time,
but shall be processed after all the active events,
like an explicit zero delay (`#0`).

3. ***Nonblocking assign update events***,
events have been evaluated in previous simulation time,
but shall be assigned at this simulation time after all the active and inactive events are processed.

4. ***Monitor events***,
like `$monitor` and `$strobe` system tasks.

5. ***Future events***,
are divided into *future inactive events* and *future nonblocking assignment update events*.

The callback procedures scheduled with PLI routines shall be treated as inactive events.

Events can be added to any of the regions,
but are only removed from the *active region*.
Active events are updated or evaluated firstly,
and the events are activated by the order of inactive events,
nonblocking assign update events, monitor events,
and then advance to next event time and active future events.


## Determinism and Nondeterminism

Determinism:
- Statements within a begin-end block shall be executed in the order in which they appear in that begin-end block.
- Nonblocking assignments (NBAs) shall be performed in the order the statements were executed.

Nondeterminism
- Active events can be taken off the Active or Reactive event region and processed in any order.
- Statements without time-control constructs in procedural blocks do not have to be executed as one event.


## Continuous Assignment

A continuous assignment statement corresponds to a process,
sensitive to the source elements in the expression.
When the value of the expression changes,
it causes an active update event to be added to the event queue,
**using current values to determine the target**.


## Blocking Assignment

A blocking assignment statement with a delay computes the right-hand side value using the current values,
then causes the executing process to be suspended and scheduled as a future event.
If the delay is 0, the process is scheduled as an inactive event for the current time.

When the process is returned (or if it returns immediately if no delay is specified),
the process performs the assignment to the left-hand side and enables any events based upon the update of the left-hand side.
**The values at the time the process resumes are used to determine the target(s).**
Execution may then continue with the next sequential statement or with other active events.


## Nonblocking Assignment

A nonblocking assignment statement always computes the updated value and schedules the update as a nonblocking assign update event,
either in this time step if the delay is zero or as a future event if the delay is nonzero.
**The values in effect when the update is placed on the event queue are used to compute both the right-hand value and the left-hand target.**

Note:

Values in effect are the values before update.


# SystemVerilog Stratified Event Scheduler


## Stratified Event Scheduler

Every event has one and only one simulation execution time.
All scheduled events at a specific time define a time slot.


The simulation regions of a time slot consist of:

1. ***Preponed Events Region***,
sampling in the preponed region is identical to sampling in the previous postponed region.

2. ***Active Events Region***,
holds current active events being evaluated.

3. ***Inactive Events Region***,
holds events to be evaluated after active events,
like an explicit zero delay `#0`.

4. ***NBA***
i.e. Nonblocking Assignment Update Events Region,
holds events to be evaluated after inactive events,
like a nonblocking assignment.

5. ***Observed Events Region***,
is for evaluation of property expressions.

6. ***Reactive Events Region***,
the code specified by blocking assignments in checkers, program blocks
and the code in action blocks of concurrent assertions are scheduled in the Reactive region.

7. ***Re-Inactive Events Region***
holds events to be evaluated after reactive events,
like an explicit zero delay `#0`.

8. ***Re-NBA Events Region***
holds events to be evaluated after re-inactive events,
like a nonblocking assignment.

9. ***Postponed Events Region***
holds `$monitor`, `$strobe`, and other events.

In addition to simulation regions,
the PLI (Programming Language Interface) regions of a time slot,
which provide a PLI callback control point
that allows PLI application routines to read values, write values or create events.

1. ***Pre-Active***,
2. ***Pre-NBA***
3. ***Post-NBA***
4. ***Pre-Observed***
5. ***Post-Observed***
6. ***Pre-Re-NBA***
7. ***Post-Re-NBA***
8. ***Pre-Postponed***


## Determinism and Nondeterminism

Determinism:
- Statements within a begin-end block shall be executed in the order in which they appear in that begin-end block.
- Nonblocking assignments (NBAs) shall be performed in the order the statements were executed.

Nondeterminism
- Active events can be taken off the Active or Reactive event region and processed in any order.
- Statements without time-control constructs in procedural blocks do not have to be executed as one event.


