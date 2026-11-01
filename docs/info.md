<!---

This file is used to generate your project datasheet. Please fill in the information below and delete any unused
sections.

You can also include images in this folder and reference them in the markdown. Each image must be less than
512 kb in size, and the combined size of all images must be less than 1 MB.
-->

## How it works

This is the initial build scaffold for a programmable protocol emulator. The
current RTL is the Tiny Tapeout example adder, used to check the CMOS5L tool flow.
The instruction engine and protocol firmware have not been implemented yet.

The circuit computes `uo_out = (ui_in + uio_in) mod 256`. The dedicated inputs
carry operand A, and the bidirectional pins carry operand B as inputs because
`uio_oe` is always zero. Clock, reset, and enable are unused in this combinational
smoke-test circuit. The pin names in `info.yaml` describe this scaffold and will
need updating when the protocol engine is implemented.

The initial physical target is 6x4 CMOS5L tiles. See `day1-verification.md` for the
official tooling revisions inspected and the remaining build checks.

## How to test

Set `ui_in` to 20 and `uio_in` to 30; `uo_out` should read 50. Inputs 255 and 1
should produce 0 because carry beyond the eighth bit is discarded.

For the automated RTL smoke test, install Icarus Verilog, GNU Make, and the Python
packages in `test/requirements.txt`, then run `make` from the `test` directory.
The GitHub `test` workflow runs the same test and checks its results.

After committing a change to GitHub, inspect the `gds` workflow. The `gds`,
`precheck`, and `gl_test` jobs must all pass before calling the minimal physical
build verified. The viewer deployment also requires GitHub Pages to be configured
for GitHub Actions.

## External hardware

No external hardware is needed for simulation. A physical smoke test needs a
compatible Tiny Tapeout board and a means of driving two 8-bit operands and
observing the 8-bit sum. Use electrical levels specified by the board. External
components for the future protocol emulator will be documented with its pin plan.
