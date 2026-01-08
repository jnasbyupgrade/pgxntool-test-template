# pgxntool-test-template

## ⚠️ DEPRECATED REPOSITORY

**This repository is no longer used and exists only to preserve its commit history.**

## What Happened

The template extension files that used to live here have been moved to:
- **[pgxntool-test/template/](https://github.com/decibel/pgxntool-test/tree/master/template)**

The test harness now creates fresh test repositories directly from that template directory, eliminating the need for a separate template repository.

## Current Architecture

The pgxntool project now uses a two-repository pattern:

1. **[pgxntool](https://github.com/decibel/pgxntool)** - The framework code
2. **[pgxntool-test](https://github.com/decibel/pgxntool-test)** - The test harness with template files

## Migration Guide

If you were using this repository:
- Template files are now in `pgxntool-test/template/`
- Tests create repositories directly from those template files
- No changes needed to existing workflows using `pgxntool-test`

## Historical Context

This repository served as a minimal PostgreSQL extension that was cloned and manipulated during testing. The approach has been simplified to use template files stored directly in the test harness repository.

---

**Repository archived on:** 2026-01-08
**Reason:** Consolidated into pgxntool-test repository
