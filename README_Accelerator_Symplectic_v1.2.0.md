# Universal 6D Accelerator Beam Dynamics Visualizer — v1.2.0

## New in v1.2.0: external particle-distribution import

The built-in matched Gaussian beam remains the default. The simulator switches to an imported distribution only after you choose a file and click **Load & simulate data**. **Restore default beam** returns to the built-in distribution.

After import, every row is treated as one particle at the entrance of the currently configured lattice. The simulator propagates those particles through the selected FODO/ring lattice using its existing element maps. The phase-space heat maps, 1σ/2σ/3σ ellipses, RF plot, 3D trajectories, selection inspector, aperture losses, and diagnostics update from the imported particles. The import panel also reports current means, RMS beam sizes/spreads, and RMS geometric transverse emittances.

## Supported input formats

- CSV, TSV, or TXT with a header row and one particle per row.
- JSON as an array of particle objects, or an object containing `particles`, `data`, or `coordinates` arrays.
- Maximum 20,000 particles per upload in this browser build.

### CSV example

```csv
x_mm,xp_mrad,y_mm,yp_mrad,z_mm,delta
1.0,0.2,-0.5,0.1,2.0,0.001
-1.2,-0.1,0.7,-0.2,-2.0,-0.001
0.5,0.05,0.3,0.08,0.4,0.0005
```

Supported coordinate aliases include `x`, `xp`/`xprime`, `y`, `yp`/`yprime`, `z`, and `delta`/`dpp`. Unit suffixes are inferred when the unit selectors are set to Auto: `x_mm`, `x_um`, `xp_mrad`, `xp_urad`, `z_mm`, etc. Unsuffixed position and slope columns default to meters and radians respectively. Delta is the dimensionless relative momentum deviation Δp/p₀.

`px` and `py` columns are also supported. In Auto mode, bare `px`/`py` columns are interpreted as normalized canonical momenta pₓ/p₀ and pᵧ/p₀, then converted into the slope representation expected by the imported-particle interface. If those columns actually contain slopes, select **Treat px/py as slopes**. `xp`/`yp` columns are interpreted as slopes.

### JSON example

```json
{
  "units": {"position": "mm", "slope": "mrad"},
  "particles": [
    {"x": 1.0, "xp": 0.2, "y": -0.5, "yp": 0.1, "z": 2.0, "delta": 0.001},
    {"x": -1.2, "xp": -0.1, "y": 0.7, "yp": -0.2, "z": -2.0, "delta": -0.001}
  ]
}
```

## Important modeling limits

- This is a **particle-distribution importer**, not a universal accelerator-file reader. It does not infer a machine lattice, magnet strengths, RF settings, or coordinate conventions from an arbitrary accelerator data file.
- The uploaded coordinates are assumed to be the initial distribution at the entrance of the currently configured lattice. If data are measured/exported at a downstream station, transform them to the lattice entrance or adapt the lattice before interpreting the result.
- Missing coordinates are set to zero. Check the import summary and column names before trusting results.
- File columns must use the declared conventions. The code does not silently reinterpret `ct`, `t`, absolute momentum, or energy as `z` or delta because their signs and definitions vary by code.
- The tracker remains a paraxial canonical element-map model. Importing measured particles does not make the underlying magnet/RF model a full 3D field-map simulation.
- Existing numerical diagnostics and derived moments are useful for exploration, but quantitative claims still require convergence checks and external benchmarking against an established code using matching conventions.

## Open

Download `Accelerator_Symplectic_Nonlinear_Simulator_v1.2.0.html` and open it in a modern browser. No server is required. Choose a file in **Import accelerator particle data**, select units/convention if necessary, then click **Load & simulate data**.
