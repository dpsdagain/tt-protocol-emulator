# Day 1 verification log

Checked on November 1, 2026. The roadmap schedules Day 1 for November 2.

## Starting point

- Repository: https://github.com/dpsdagain/tt-protocol-emulator
- Branch: `cmos5l`
- Starting commit: `9c35f6ff9b3634023db7f69f4b2c1a4b742d6176`
- Current RTL: the unmodified combinational 8-bit example adder. The protocol
  engine is future work.
- The existing workflow already uses `TinyTapeout/tt-gds-action@ihp-cmos5l`
  with `pdk: ihp-sg13cmos5l`.

## Official template and size verification

The build action's default support-tools reference is `ihp-sg13cmos5l`.

- [Build action inspected at commit 3412659307918422f3f0727917cf9b499aaca588](https://github.com/TinyTapeout/tt-gds-action/blob/3412659307918422f3f0727917cf9b499aaca588/action.yml)
- [Official CMOS5L tile sizes at support-tools commit d66cf179e7bc4d296362ab7e2e3b344dc3c4f665](https://github.com/TinyTapeout/tt-support-tools/blob/d66cf179e7bc4d296362ab7e2e3b344dc3c4f665/tech/ihp-sg13cmos5l/tile_sizes.yaml)
- [6x4 physical template](https://github.com/TinyTapeout/tt-support-tools/blob/d66cf179e7bc4d296362ab7e2e3b344dc3c4f665/tech/ihp-sg13cmos5l/def/tt_block_6x4_pgvdd.def)
- [8x4 physical template](https://github.com/TinyTapeout/tt-support-tools/blob/d66cf179e7bc4d296362ab7e2e3b344dc3c4f665/tech/ihp-sg13cmos5l/def/tt_block_8x4_pgvdd.def)

Both 6x4 and 8x4 are defined in the inspected official tooling, and both have
physical DEF templates. This establishes tooling availability, not a passing
build or a competition approval. The selected baseline is **6x4**, as planned in
the roadmap; no migration to 8x4 is being made.

The old `info.yaml` size comment was incomplete for this CMOS5L tooling. The
6x4 CMOS5L die area in the official size table is `0 0 1289.28 710.64` micrometers.

## Reproduced configuration errors and local changes

Running the official `ProjectInfo` validator on the starting `info.yaml` reports:

```text
Project title cannot be empty
Project author cannot be empty
Project description cannot be empty
```

These are confirmed build-configuration blockers. The historical GitHub job log
was not accessible without authentication, so these errors are not claimed as a
complete diagnosis of that earlier run.

The local fix fills in those fields, selects 6x4, names pins according to the
existing adder circuit, and replaces the datasheet placeholders. The pin labels
are temporary scaffold documentation, not the final protocol pin plan.

## Validation results

- Official `check_project_docs` validation with `ihp-sg13cmos5l`: **passed**.
- Selected size accepted and matching physical DEF template present: **passed**.
- Icarus Verilog compile of `src/project.v` with top `tt_um_example`: **passed**.
- Full RTL simulation: **not rerun locally**; cocotb and GNU Make are unavailable
  in this local environment. The existing GitHub test run passed at the starting
  commit, and this fix does not change RTL or tests.
- Full GDS hardening, timing, precheck, and gate-level simulation: **not yet
  verified for these changes**.

The [historical GDS run](https://github.com/dpsdagain/tt-protocol-emulator/actions/runs/38541385036)
failed in `Build GDS`; precheck was skipped. Its public job metadata is available,
but downloading its full log returned HTTP 403 without authentication.

## Finish verification on GitHub

1. Sign in to GitHub, open this repository, and select branch `cmos5l`.
2. Upload the updated root `info.yaml` and the `docs` folder containing
   `info.md` and this log, preserving their paths. Commit the changes.
3. Open **Actions** and select the new `gds` run associated with that commit.
   A commit triggers the workflow automatically. Alternatively, choose the
   `gds` workflow, **Run workflow**, and branch `cmos5l`.
4. Confirm that `gds`, `precheck`, and `gl_test` pass. Also check the `test` and
   `docs` workflows. The viewer job requires GitHub Pages with source set to
   GitHub Actions.
5. If a job fails, expand its first failed step and copy the first substantive
   error with its surrounding lines. A generic exit-code message alone will
   not identify the cause.
6. Record the new run URL, commit, and results here. Day 1 remains incomplete
   until the working minimal physical build is verified.
