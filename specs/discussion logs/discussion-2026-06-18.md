# Discussion Log - 2026-06-18

# Topic: Development Preparation Baseline

## Decisions

* The Sage-X implementation baseline will use C# and .NET 10.
* Persistence should target Microsoft SQL Server where possible.
* JSON artifacts remain central to the platform.
* JSON schema validation should use Schema.NET / JSON Schema.NET style tooling.
* Testing will use xUnit.
* Serialization should use `System.Text.Json`.
* Logging should use `Microsoft.Extensions.Logging`.
* The first control surface should be a .NET console application.
* ASP.NET API exposure can come later.
* Trace and event records may start as JSONL or SQL-backed records.
* OpenTelemetry remains a later integration target.

## Repository Direction

The repository should begin as a local-first modular monolith with a standard `.sln` structure and service-ready module boundaries.

Expected top-level folders:

* `/src`
* `/tests`
* `/schemas`
* `/fixtures`
* `/artifacts`
* `/docs`
* `/tools`

## Immediate Preparation Goal

Before autonomous runtime behavior, build the development foundation:

1. repository skeleton
2. schema loader
3. schema definition index
4. schema validation wrapper
5. initial fixtures
6. xUnit contract tests

## Notes

The implementation plan and execution task graph remain the governing planning artifacts. This discussion log records the working agreement so future development can proceed without rereading the broader specification set.

