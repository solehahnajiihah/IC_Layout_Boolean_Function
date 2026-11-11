# Five-input static CMOS logic gate

This project implements and simulates a five-input combinational gate using a complementary CMOS pull-up and pull-down network. It addresses the schematic, timing, layout, and post-layout analysis requested in the two project milestones. The Boolean function represented by the transistor netlists is


Y = (A B C D + E


The four-input product term is kept as a transistor stack as part of project requirement, not to simplify the assigned Boolean expression.

## Circuit design

- **Pull-down network:** four NMOS transistors controlled by `A`, `B`, `C`, and `D` in series, in parallel with one NMOS controlled by `E`. The output is pulled low when `ABCD = 1` or `E = 1`.
- **Pull-up network:** four PMOS transistors controlled by `A`, `B`, `C`, and `D` in parallel, in series with one PMOS controlled by `E`. This is the dual of the pull-down network.
- **Device count:** five NMOS and five PMOS transistors.
- **Schematic dimensions:** the exported schematic uses `L = 0.18 µm` for all transistors, `W = 4 µm` for the four NMOS transistors in the series path, `W = 1 µm` for the `E` NMOS, and `W = 4 µm` for all PMOS transistors. These are the dimensions in [`project_sch.spi`](project_sch.spi); a voltage transfer curve demonstrating matched rise and fall times is not included in the available results.

The expected static behavior is `Y = 0` for every input combination with `E = 1`, and also for `A = B = C = D = 1` when `E = 0`. For all other combinations, `Y = 1`. During switching, propagation delay depends on the active path and parasitic loading. The four-device NMOS stack is a likely contributor to slower falling transitions for some stimuli; the measured cases below should be used for the actual delay comparison.

## Tools and project files

The files supplied in this repository identify **Electric VLSI 9.08** (`mocmos`/MOSIS technology) and **LTspice 26.1.1**, with T58F BSIM3 model parameters in the SPICE decks. The exported schematic and extracted layout specify `L = 0.18 µm`. 

| File | Purpose |
| --- | --- |
| [`poject_layout.jelib`](poject_layout.jelib) | Electric library containing schematic and layout views. The `poject` spelling is the existing filename. |
| [`project_sch.spi`](project_sch.spi) | Schematic-level transistor netlist and device models. Its active stimulus section currently sets all inputs and `Vdd` to 0 V for a static-power run; the pulse sources are commented out. |
| [`poject_layout.spi`](poject_layout.spi) | Parasitic-extracted layout netlist, device models, and active 1.8 V transient stimuli. |
| [`project_sch.raw`](project_sch.raw), [`poject_layout.raw`](poject_layout.raw) | Saved LTspice waveform data. |
| [`project_sch.log`](project_sch.log), [`poject_layout.log`](poject_layout.log) | LTspice simulation logs. |
| [`project.spi`](project.spi) | Separate earlier layout export; its `L = 0.4 µm`, `W = 0.6 µm` devices differ from the extracted netlist above, so it should not be mixed with the reported dimensions. |
| [`DRC`](DRC) | Electric MOSIS design-rule **deck**, not a DRC result report. |

## Supplied timing results

| Output transition | Best reported delay | Worst reported delay |
| --- | ---: | ---: |
| High to low, `tPHL` | 18.69 ps | 298.90 ps  |
| Low to high, `tPLH` | 181.71 ps | 615.82 ps |

The worst case was labeled `A-B-C: 1, D: 0, E: 1 → 0` in the measurement notes. The worst case was labeled `A-B-C: 0, E: 0 → 1`. 

The recorded simulation summary reports an average propagation delay of **249.48 ps** and average power of **49.511 µW**. The observed waveforms show periodic transitions on inputs `A` through `E`, corresponding output switching, and supply-power activity over a 0–100 ns view. In the active extracted netlist, the inputs are driven by 0–1.8 V pulse sources with different periods, and the transient analysis runs for 200 ns.

## Layout, verification, and figure of merit

The Electric library records a prior DRC and LVS check, as shown in picture below. 

<img width="508" height="182" alt="image" src="https://github.com/user-attachments/assets/b9b26b0a-7fb7-47eb-a5f6-8fedeb0a5daf" />

## Project Schematic
<img width="589" height="732" alt="image" src="https://github.com/user-attachments/assets/97d689e7-78fa-453f-bc9e-c8d477ee06b1" />

## Project Layout 
<img width="880" height="695" alt="image" src="https://github.com/user-attachments/assets/1fa42253-0948-45c4-a379-87e2c6cebacb" />





## Reproducing the available simulation

1. Open [`poject_layout.jelib`](poject_layout.jelib) in Electric VLSI to inspect the schematic and layout.
2. Open [`poject_layout.spi`](poject_layout.spi) in LTspice to run the extracted transient simulation. Its active sources use `Vdd = 1.8 V` and pulse inputs; the commented section provides an alternate static-power setup.
3. Open [`project_sch.spi`](project_sch.spi) for the schematic netlist. To run its earlier pulse-based transient setup, enable the commented 1.8 V supply, pulse sources, and transient directive, and disable the active zero-volt static-power setup.
4. Use the saved `.raw` files to inspect waveforms and the `.log` files to inspect simulator messages. Record the input and output 50% crossing times, supply-current averaging interval, and process/area scale when updating the timing and FOM results.

