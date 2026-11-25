# ALU — 3-bit Opcode, 32-bit Operand ALU
 
A combinational Arithmetic Logic Unit implementing 8 operations selected by a
3-bit opcode, operating on two 32-bit inputs.
 
## Module: `ALU`
 
```verilog
module ALU (
  input  [2:0]  Op_code,
  input  [31:0] A, B,
  output reg [31:0] Y
);
```
 
### Ports
 
| Signal    | Dir | Width | Description        |
|-----------|-----|-------|---------------------|
| `Op_code` | in  | 3     | Operation select    |
| `A`, `B`  | in  | 32    | Operands            |
| `Y`       | out | 32    | Result (registered in an always block, but logic is purely combinational — `always @(*)`) |
 
### Opcode Table
 
| Op_code | Operation      | Y          |
|---------|----------------|------------|
| `000`   | Pass A         | `A`        |
| `001`   | Add            | `A + B`    |
| `010`   | Subtract       | `A - B`    |
| `011`   | Bitwise AND    | `A & B`    |
| `100`   | Bitwise OR     | `A \| B`    |
| `101`   | Increment      | `A + 1`    |
| `110`   | Decrement      | `A - 1`    |
| `111`   | Pass B         | `B`        |
| other   | (undefined)    | `0`        |
 
## Known limitations (not addressed in the current module)
 
- No carry-out, overflow, or zero flag — any downstream logic that needs
  status flags has to derive them externally from `Y`, `A`, and `B`.
- The `default: Y = 32'b0` case silently masks illegal opcodes rather than
  asserting or flagging an error — fine for a lab exercise, not fine if this
  ever sits behind untrusted control logic.
- Opcodes are hardcoded 3-bit literals in the `case` statement rather than
  named `localparam`s (e.g. `ALU_ADD = 3'b001`), which makes the testbench
  and any consuming module harder to keep in sync with this file if the
  encoding ever changes.
## Testbench
 
`AAC2M4H1_tb.vp` — referenced in the ModelSim project as the top-level
simulation module (`AAC2M4H1_tb`), instantiating `ALU` as `DUT`. It drives
`A_tb`, `B_tb`, `Op_code_tb`, compares against an expected-value ROM
(`rom`, `vector`), and tracks results via `ErrorCount`, `FirstError`,
`ValidCheck`, and `score`. This has the shape of an auto-generated
grading/vector-check testbench rather than a hand-written one — worth
confirming its provenance before treating it as a spec.
 
## Simulation
 
Tooling: **ModelSim — Intel FPGA Starter Edition 10.5b**
 
```
vsim work.AAC2M4H1_tb
add wave -position end sim:/AAC2M4H1_tb/*
run -all
```
 
Last recorded run (from the transcript):
 
```
# This simulation is complete
# ** Note: $stop : C:/VLSI_summer/verilog/AAC2M4H1_tb.vp(118)
# Time: 1291 ns  Iteration: 0  Instance: /AAC2M4H1_tb
# Break in Module AAC2M4H1_tb in file C:/VLSI_summer/verilog/AAC2M4H1_tb.vp
```
 
At that point `ErrorCount = 0`, `FirstError = 0`, `ValidCheck` all 1s — no
failures logged up to the point the simulation stopped. This isn't a
substitute for reading the actual vector count in `rom`/`vector` to confirm
full coverage.
 
## Repo structure (assumed — confirm against your actual layout)
 
```
.
├── ALU.v                # DUT
├── AAC2M4H1_tb.vp        # testbench
└── README.md
```
 
## License / Author
 
Renesha Sagar
