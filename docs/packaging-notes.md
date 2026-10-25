# Packaging and verification record

Prepared on 25 October 2026 from the supplied `pipe_flow` case. The source folder was not modified.

## Retained inputs and evidence

- The five initial fields, their initial-conditions include, transport and turbulence properties, and required meshing/solver dictionaries.
- Both `0/` and `0.orig/`. Their original U files differed only in whitespace around the wall boundary.
- The source STL, unchanged, in `geometry/pipe-original.stl`.
- Original assessment PDF, four PNG images, the complete small solver log, and five patch-average data files.

## Reproducibility repairs

1. Replaced the inherited motorBike `Allrun`, which copied unrelated geometry and referenced a missing `system/decomposeParDict.6`.
2. Replaced the inherited `Allclean`, which would delete the input geometry directory. The new cleaner requires `--yes` and retains input geometry.
3. Changed the background boundary type from `walls` to the valid `wall` type.
4. Enabled castellation and snapping as well as layer addition. The saved source configuration only enabled layer addition and depended on an already-generated mesh.
5. Grouped the original STL triangles into four named regions, preserving every triangle coordinate and normal. End faces were identified by all three vertices lying within 1e-8 m of x=0 (inlet), y=-0.2 (outlet-01), or x=0.45 (outlet-02). Counts: 178 triangles per end face and 1,270 wall triangles; 1,804 total. Added explicit region names and patch types to snappyHexMeshDict.
6. Removed the duplicate inlet include from both U files; the identical explicit inlet entry remains. Removed unused include files from the package.
7. Added LF line-ending rules for Linux compatibility. Run through `sh` so executable file modes are not required after ZIP extraction.

No changes were made to viscosity, prescribed field values, turbulence model, solver schemes, refinement levels, layer controls, or the 500-iteration limit.

## Excluded from the distributable

Generated time folders 100–500, VTK exports, the existing polyMesh, feature meshes and extraction outputs, empty ParaView marker, unrelated Python exercises/notebooks and heat-conduction coursework, Windows download metadata, unused motorBike post-processing dictionaries, and duplicate/unused parallel configuration files. These remain in the original source folder.

The reproducible geometry stays at the path required by the case. The unchanged source STL is also retained separately to make the boundary-label repair auditable.

## Validation

Validation is performed in an independent temporary Linux folder with OpenCFD OpenFOAM v2412 (patch 260127). Full paths containing spaces are rejected by this OpenFOAM installation; the README therefore uses a Linux home-directory path.

The rebuilt mesh contains 183,378 cells. `checkMesh` reports `Mesh OK`, maximum non-orthogonality 63.5633 degrees (average 3.66232), and maximum skewness 2.58858. A direct comparison confirmed that the region-labelled STL contains the same 1,804 triangle records as the supplied STL, with no added or removed triangles. All local dictionary includes resolve.

The original report and images describe the historical run. Verification logs for the repaired workflow are kept separately from that historical evidence. Reproduction of the original exact mesh or a claim of physical validation is outside the evidence provided by these files.

### Completed execution check

The complete `sh Allrun` workflow exited successfully: feature extraction, background meshing, full snappyHexMesh, checkMesh, and all 500 simpleFoam iterations. Logs are retained in `results/verification/`. This demonstrates executability, not converged or experimentally validated physics.

Final solver excerpt:

```text
Time = 500

smoothSolver:  Solving for Ux, Initial residual = 0.000140098, Final residual = 1.19579e-05, No Iterations 9
smoothSolver:  Solving for Uy, Initial residual = 0.00393521, Final residual = 0.000323903, No Iterations 9
smoothSolver:  Solving for Uz, Initial residual = 0.0148519, Final residual = 0.00148448, No Iterations 9
GAMG:  Solving for p, Initial residual = 0.00559177, Final residual = 4.92712e-05, No Iterations 5
time step continuity errors : sum local = 0.000676421, global = -4.69459e-06, cumulative = 0.143833
smoothSolver:  Solving for omega, Initial residual = 1.17276e-05, Final residual = 6.27628e-07, No Iterations 3
smoothSolver:  Solving for k, Initial residual = 0.00195255, Final residual = 9.14719e-05, No Iterations 4
ExecutionTime = 217.71 s  ClockTime = 218 s

End
```
