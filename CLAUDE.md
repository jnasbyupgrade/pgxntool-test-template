# CLAUDE.md

## Repository Status: DEPRECATED

**This repository is deprecated and no longer used.** It is kept only to preserve its commit history.

## What Happened

The template extension files that used to live in this repository have been moved to `../pgxntool-test/template/`. The test harness now creates fresh test repositories directly from that template directory, eliminating the need for a separate template repository.

## Migration

If you need the template files, they are now located at:
- **../pgxntool-test/template/**

The test harness (`../pgxntool-test/`) contains all testing infrastructure and template files in a single repository.

## Two-Repository Pattern

The pgxntool project now uses a simpler two-repository pattern:

1. **../pgxntool/** - The framework code that gets embedded into extension projects
2. **../pgxntool-test/** - The test harness (includes template files in `template/` directory)

## Historical Context

This repository previously served as a minimal "dummy" PostgreSQL extension used as a test subject. Tests would clone this repository and manipulate it to validate pgxntool's functionality. This approach has been replaced with a simpler method of creating fresh test repositories from template files stored directly in the test harness.
