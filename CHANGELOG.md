# Changelog

Dates are UTC. One section per release, what changed and why, so a reader of the diff has the
sentence beside it.

## 0.1.0-pre.1

- First version.
- `request` and `bring_up_failure` return a `Result` and refuse an out-of-range opcode or step in a release build as well as a debug build, instead of panicking in one and accepting it in the other.

## BUGS

- The template's lag check guards the CI pin, not this file; a change that matters to a user of
  the crate belongs in a release line here.
