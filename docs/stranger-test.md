# The stranger cadence

The release's whole claim is that a stranger, given this repository and nothing else, can build
the crate and run its proofs from the README alone. A real agent runs that check on a
cadence, and the weekly `stranger cadence` job fails when the
last run is past 28 days.

What a run is:

1. Clone the repository fresh.
2. Follow the README and nothing else: build, test, and run the proofs.
   The README must name the build and test commands (with any feature a test target needs), the
   bare-metal build with `rustup target add`, the Kani install before the first `cargo kani`, how
   long the proofs take and how to run one harness, what a passing run prints that looks alarming,
   how to run and revert one falsification patch, and how to check the minimum Rust version.
   Each one it lacks is a finding.
3. Note every place the README lied, omitted, or assumed. Those are findings against the README,
   not against the stranger.
4. Record the date on the line below.

Last run: 2026-11-26

## What the last run did

A fresh clone and the README, with empty Cargo, rustup and home directories, on a shared 80-core
x86_64 Linux host. Rust came from rustup.rs, and the run followed "Building and testing", "Running
the proofs", "Running one falsification patch", "Using it" and "Minimum Rust version" command by
command.

- `cargo test` passes, and `cargo build --lib` passes for `x86_64-unknown-none`,
  `aarch64-unknown-none-softfloat` and `riscv64imac-unknown-none-elf`.
- The Kani install took 4 seconds and setup 23. Kani then held 485 megabytes in `~/.kani` and its
  nightly toolchain 1.5 gigabytes in `~/.rustup`.
- `cargo kani` ends with `Complete - 7 successfully verified harnesses, 0 failures, 7 total.` and
  exits 0, in about one minute at a peak of 2.2 gigabytes. One harness by its full path takes
  about 4 seconds, and Kani refuses a bare name.
- With the `readiness_is_reported_exactly_when_a_first_byte_is_nonzero` falsification applied,
  that harness fails with `assertion failed: word == READY` and exits 1. The reversed patch
  leaves `git status` clean. Each of the other six patches also turns its harness red.
- A scratch crate that depends on this repository by `git` and `rev` builds and runs `fill_with`.
- At Rust 1.88, the stated minimum, the tests and the three bare-metal builds pass.

The run found three things. `cargo kani` printed a future-incompatibility warning,
`trailing semicolon in macro used in expression position`, that the README did not list among
the alarming lines; the proof arm it came from is now a block, and the warning is gone. Three
falsification patches applied 123 lines off; this version rebases them. The README did not give the type of
the closure `fill_with` takes; it now does.

Result: pass on the build, the tests and the proofs, with one omission in the README, one
warning from the code and three stale patches. This version corrects each of them, and a second fresh clone of it
followed the README to the same results with neither.
