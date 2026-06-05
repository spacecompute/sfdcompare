---
name: sfdcompare
description: Reference and update OreKit vs GMAT vs 42 vs Nyx feature comparisons, variable mappings, architecture details, and defaults.
argument-hint: [compare | update | lookup | architecture]
---

# Space Flight Dynamics Comparison

Reference and update the OreKit vs GMAT vs 42 vs Nyx feature comparison maintained in `README.md`. All comparison data lives in a single file.

## Project Structure

| File | Purpose |
|------|---------|
| `README.md` | All comparison data: feature tables, variable mappings, defaults, architecture, diagrams, WASM analysis, roadmap |
| `LICENSE` | GNU GPLv3 |

## README Sections Reference

| Section | Content |
|---------|---------|
| Feature Comparison (Tables 1-17) | Side-by-side capability matrix: propagation, force models, coordinates, orbit rep, time, maneuvers, solvers, events, OD, spacecraft modeling, attitude, sensors/actuators, file formats, visualization, scripting, special capabilities, RF/communications modeling |
| Summary Comparison | High-level overview table (purpose, architecture, best-for, strengths) |
| Variable Name Mapping | Cross-reference of variable names across all four tools (orbital elements, propagation params, integrators, frames, spacecraft properties, attitude, sensors, actuators) |
| Gravitational Parameters | Standard constants for Earth, Sun, Moon, Mars, Jupiter |
| Default Values | Propagator defaults and force model defaults |
| Source Code Analysis | Architecture deep-dives for OreKit (Java), GMAT (C++), 42 (C), Nyx (Rust) |
| Architecture Diagrams | Mermaid diagrams for each tool and unified architecture |
| Unified Architecture Proposal | Design for integrating all four tools via REST/Python/WASM API |
| Implementation Roadmap | 4-phase plan with technology stack |
| WASM Feasibility Analysis | Compilation feasibility for each tool to WebAssembly |

## Tool References

| Tool | Repository | Docs |
|------|-----------|------|
| **OreKit** | [GitLab](https://gitlab.orekit.org/orekit/orekit) / [GitHub](https://github.com/CS-SI/Orekit) | [API 13.1.3](https://www.orekit.org/site-orekit-13.1.3/apidocs/index.html) |
| **GMAT** | [GitHub](https://github.com/nasa/GMAT) | [Wiki](https://gmat.atlassian.net/wiki/spaces/GW/overview) |
| **42** | [GitHub](https://github.com/ericstoneking/42) | [NASA Software Catalog](https://software.nasa.gov/software/GSC-16720-1) |
| **Nyx** | [GitHub](https://github.com/nyx-space/nyx) | [nyxspace.com](https://nyxspace.com/), [docs.rs](https://docs.rs/nyx-space/latest/nyx_space/) |

## Commands

### `compare` — Look up feature comparisons

Read the relevant feature comparison table(s) from `README.md` and present the requested comparison. Tables are numbered 1-16 in the Feature Comparison section.

### `update` — Apply corrections to comparison data

When applying corrections, follow the Update Workflow below to ensure all affected sections are updated consistently.

### `lookup` — Find variable mappings, defaults, or struct definitions

Search the Variable Name Mapping tables, Default Values, or Key Data Structures sections for the requested information.

### `architecture` — Reference tool architecture details

Read the Source Code Analysis section for the requested tool (OreKit, GMAT, 42, or Nyx). Includes directory structure, key data structures, code examples, and design philosophy.

## Update Workflow

When updating any 42/OreKit/GMAT/Nyx data, check and update ALL affected locations:

1. **Feature comparison tables** (17 tables in the Feature Comparison section)
2. **Summary Comparison table**
3. **Variable Name Mapping tables** (orbital elements, propagation params, integrators, frames, spacecraft properties, attitude)
4. **Default Values** (propagator defaults, force model defaults)
5. **Source Code Analysis** (directory structure, data structures, input file format, IPC)
6. **Mermaid diagrams** (individual tool diagrams, unified architecture diagram, data flow diagram)
7. **Spacecraft Modeling table** (drag/SRP properties)

## Key Conventions for 42

These conventions are based on verified feedback from the 42 developer:

| Topic | Correct Information |
|-------|-------------------|
| SCType definition | `Include/42types.h` (NOT `AcTypes.h`) |
| OrbitType definition | `Kit/Include/orbkit.h` |
| OrbitType regime field | `Regime` (NOT `Type`); values: `ORB_ZERO`, `ORB_FLIGHT`, `ORB_CENTRAL`, `ORB_THREE_BODY` |
| SC input files | Named `SC_*.txt` (e.g. `SC_Simple.txt`), NOT `Inp_SC.txt` |
| Input file format | Value-first: `value  ! description` (NOT `key: value`) |
| Atmospheric model | NRLMSISE-00 (primary for Earth), Exponential (Mars) |
| Reflectivity | `SpecFrac`, `DiffFrac` (NOT `Cr`) |
| Drag/SRP areas | Recomputed per timestep (no global `DragArea` or `SRPArea`) |
| Mass decrement | 42 does NOT automatically decrement mass |
| TAI time | Bookkeeps as `AtomicTime` |
| Ephemeris | Meeus algorithms + DE430/DE440 from SPICE (hosted as local text files) |
| TLE support | Parses TLEs, converts to other formats for propagation |
| Euler angles | All 12 sequences supported |
| Kalman filter | Starter filter in `Kit/Source/fswkit.c` |
| Range/range-rate | Supported via `Source/42commlink.c` |
| Ground track | Map window includes ground track |
| IPC marshalling | Auto-generated from header markup (`[~>~]`, `[~<~]`, `[~=~]`) via Julia scripts in `MetaCode/` |
| Socket utilities | `InitSocketServer`, `InitSocketClient` in `Kit/Source/iokit.c` |
| AcType (FSW struct) | Defined in `Include/AcTypes.h` (distinct from SCType) |
| RF/CommLink | Full link budget in `Source/42commlink.c`; configured via `Inp_CommLink.txt`; supports uplink, downlink, crosslink; antenna patterns from OBJ mesh files; references Ippolito (Wiley 2008) and Wertz (New SMAD 2011) |

## Key Conventions for GMAT

These conventions are based on verified feedback from GMAT engineers:

| Topic | Correct Information |
|-------|-------------------|
| Primary purpose | Mission design, trajectory optimization, AND orbit determination |
| RK4 integrator | Classical RK4 with no error control (distinct from higher-order RK methods) |
| Ocean tides | NOT supported |
| STK ephemeris | Can generate and propagate .e files |
| Python bindings | Via SWIG interface (not experimental) |
| Java interface | Supported via interface |
| Plugin system | Custom C++ plugins |
| Monte Carlo | Supported via scripting |
