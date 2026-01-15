# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Git Commit Guidelines

**IMPORTANT**: When creating commit messages, do not attribute commits to yourself (Claude). Commit messages should reflect the work being done without AI attribution in the message body. The standard Co-Authored-By trailer is acceptable.

## What This Repo Is

**pgxntool-test-template** is a minimal "dummy" PostgreSQL extension that serves as a test subject for **../pgxntool-test/** (the test harness).

This repo is **NOT intended to be used as a real extension**. It exists solely to:
1. Demonstrate proper pgxntool integration
2. Provide a known-good starting state for automated tests
3. Serve as a template that tests clone and manipulate

## The Three-Repository Pattern

- **../pgxntool/** - The build framework (embedded via git subtree)
- **../pgxntool-test/** - The test harness that clones this repo and validates pgxntool
- **pgxntool-test-template/** (this repo) - The test subject (minimal working extension)

**Testing flow**: pgxntool-test clones this repo → runs pgxntool operations → validates outputs

## Repository Structure

```
pgxntool-test-template/
├── pgxntool-test.control   # Extension control file
└── t/                      # Embedded pgxntool + extension code
    ├── doc/
    │   └── TEST_DOC.asc    # Sample documentation
    ├── sql/
    │   └── pgxntool-test.sql    # Extension SQL (simple add function)
    ├── test/
    │   └── input/
    │       └── pgxntool-test.source  # Test file (loads extension, runs simple test)
    └── .gitignore
```

**Important**: This repo has a subdirectory structure (everything in `t/`) which is unusual. Most real extensions using pgxntool would have files at the root level. This structure is specific to the testing setup.

## What the Extension Does

The extension provides a single trivial function:

```sql
CREATE FUNCTION "pgxntool-test"(a int, b int) RETURNS int
```

It simply adds two integers. The function is intentionally minimal because the focus is testing pgxntool's build/test infrastructure, not extension functionality.

## How pgxntool-test Uses This Repo

The test harness (**../pgxntool-test/**) performs these operations:

1. **Clone**: `git clone` this repo to a temp directory
2. **Isolate**: Rewire git remote to a fake repo (prevents accidental pushes)
3. **Inject pgxntool**: Either via `git subtree add` or `rsync` (if testing uncommitted changes)
4. **Run setup**: Execute `pgxntool/setup.sh`
5. **Build/Test**: Run `make`, `make test`, `make dist`, etc.
6. **Validate**: Compare outputs against expected results

## Expected Test Behavior

When used in tests, this extension should:
- Successfully run `pgxntool/setup.sh` (when repo is clean)
- Build without errors
- Pass its single test (verifying 1 + 2 = 3)
- Generate proper META.json from META.in.json template
- Create distribution .zip file
- Generate HTML from TEST_DOC.asc

## Maintenance Guidelines

**Changes to this repo should be rare** because:
1. It's a test fixture - stability is more important than features
2. Changes may require updating expected outputs in **../pgxntool-test/expected/**
3. It should remain minimal to make test failures easy to diagnose

**When to modify**:
- Adding test coverage for new pgxntool features
- Fixing bugs in the test fixture itself
- Updating for PostgreSQL version compatibility

**When modifying**:
1. Make changes here
2. Run full test suite: `cd ../pgxntool-test && make test`
3. Verify all tests still pass OR update expected outputs if intentional
4. Document what changed and why

## File Details

### pgxntool-test.control
Standard PostgreSQL extension control file. Specifies:
- Extension name: `pgxntool-test`
- Default version, comment, etc.

### t/sql/pgxntool-test.sql
The extension's SQL code. Contains a simple addition function that works on PostgreSQL 9.1+ (avoids named parameters for old version compatibility).

### t/test/input/pgxntool-test.source
Test file using pg_regress format:
- Loads pgTap via pgxntool's setup infrastructure
- Loads extension deps via `test/deps.sql`
- Creates extension `pgxntool-test`
- Runs one test verifying the add function works
- Uses pgTap `plan()`, `is()`, and `finish()` functions

### t/doc/TEST_DOC.asc
Minimal Asciidoc file for testing document generation. pgxntool should auto-detect and convert to HTML.

## Not Present (But Would Be in Real Extensions)

Real extensions using pgxntool typically have:
- `Makefile` (just `include pgxntool/base.mk`)
- `META.in.json` (metadata template)
- `src/*.c` (optional C code)
- Actual functionality beyond a trivial add function
- Comprehensive test suite
- Real documentation

These are omitted or minimal here because they're created/validated by the test harness itself.

## Related Repositories

- **../pgxntool/** - The build framework being tested
- **../pgxntool-test/** - The test harness that uses this repo as a test subject

## Special Notes

- **Git subtree**: This repo expects pgxntool to be added via `git subtree add -P pgxntool`
- **Clean repo required**: Tests verify that setup.sh properly rejects dirty repos
- **Version compatibility**: SQL code avoids features requiring newer PostgreSQL versions
- **Fake asciidoc**: Tests may use a fake asciidoc implementation to avoid dependency variability
