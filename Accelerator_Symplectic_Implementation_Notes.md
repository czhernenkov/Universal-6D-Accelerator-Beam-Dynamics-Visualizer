# Accelerator Symplectic Nonlinear Simulator — v1.1.1

## Release status

**Version:** 1.1.1  
**Type:** standalone HTML simulator + in-app numerical validation suite  
**Tracking model:** canonical paraxial element maps; fourth-order Yoshida composition for quadrupoles; thin Hamiltonian multipole kicks; sinusoidal RF energy kicks  
**RK4:** removed from the active simulator source  
**External MAD-X/Bmad benchmark:** not yet run

## Main references

1. **USPAS Accelerator Physics I (UCSC, 2008)**, course notes by Vladimir Litvinenko, Eduard Pozdeyev, and Todd Satogata. Foundation for canonical variables, accelerator maps, and the symplectic condition \(M^T S M=S\).  
   https://uspas.fnal.gov/materials/08UCSC/Accelerator_Physics1.pdf

2. **Waldo MacKay, “Thoughts on Healy’s Symplectification Algorithm,” BNL C-A/AP/215 (2005).** This note concerns symplectifying an almost-symplectic matrix and possible singular cases. It is a matrix-validation reference, not the particle integrator used here.  
   https://technotes.bnl.gov/PDF?publicationId=31958

## Additional external references

3. **H. Yoshida, “Construction of higher order symplectic integrators,” Physics Letters A 150 (1990), 262–268.** Fourth-order composition used for quadrupole drift–kick–drift substeps.  
   https://doi.org/10.1016/0375-9601(90)90092-3

4. **E. Forest and R. D. Ruth, “Fourth-order symplectic integration,” Physica D 43 (1990), 105–117.**  
   https://doi.org/10.1016/0167-2789(90)90019-L

5. **Forest, “Symplectic integration” (1987), Particle Accelerators.** Accelerator-specific background on symplectic integration and split-operator tracking.  
   https://cds.cern.ch/record/170941/files/p15.pdf

6. **Bmad manual and tutorials.** A modern accelerator tracking framework; its available tracking methods depend on element and problem.  
   https://www.classe.cornell.edu/bmad/basic_manual.html

## v1.1.1 fixes

- The integration-step control now determines the element subdivision. The previous override silently used fixed element-specific slice lengths regardless of the control value.
- RF cavity voltage is distributed over its slices using normalized weights derived from the integrated standing-wave profile. The slice voltages sum to the requested cavity voltage instead of applying the full voltage on every slice.
- The first-order map used by closed-orbit/export/diagnostic routines is now composed from the same micro-slice sequence as the tracker. This fixes a mismatch caused by comparing a full-element diagnostic map with a tracker that applies longitudinal slip in each bend/drift slice.
- The RF linearization accepts the per-slice voltage, frequency, and synchronous phase.
- Added an in-app **Run validation suite** button. It reports element-map determinant/symplectic checks, finite-difference Jacobian symplecticity at the origin and at finite amplitude, state finiteness, and agreement with the assembled linearized map.
- Kept the clickable particle inspector and existing plots. The default browser smoke test confirmed particle selection and inspector rendering.

## Validation results observed for v1.1.1

The following checks were run in headless Chromium on 2026-10-10. The results are also provided as JSON files in this package.

| Check | Result | Interpretation |
|---|---:|---|
| HTML/JavaScript syntax | Pass | Inline JavaScript passed `node --check` after extraction from the HTML. |
| Browser runtime errors | 0 | No page errors or console errors during the default-load smoke test. |
| Default tracking | 100 particles; 51 elements; 2,137 micro-slices; 2,138 frames | All stored tracked states were finite. |
| Clickable particle inspector | Pass | Dispatching a particle click selected particle 0 and displayed the inspector. |
| Drift / quadrupole / sector linear-map determinants | Errors ≤ 1.1×10⁻¹⁶ from unity | Basic matrix checks. |
| Individual drift / quadrupole / sector symplectic residuals | ≤ 1.4×10⁻¹⁵ | Checks use the code's declared canonical matrix \(S\). |
| Full-tracker finite-difference Jacobian at origin | \(\det M=1.000000000000078\); residual \(2.54\times10^{-13}\) | Strong local numerical check, not a proof of physical-model correctness. |
| Full-tracker Jacobian at finite amplitude | Residual \(2.87\times10^{-9}\) | Finite-difference result; depends on perturbation size and conditioning. |
| Tracker Jacobian vs assembled linearized micro-map | Maximum absolute coefficient difference \(4.94\times10^{-8}\) at 20 mm target step | Linearization and tracking now agree closely in this baseline case. |
| Step-halving study | 20 → 10 → 5 mm | Maximum tracker-vs-linear-map difference: 4.94×10⁻⁸ → 3.09×10⁻⁹ → 1.93×10⁻¹⁰, approximately 16× reduction per halving, consistent with fourth-order convergence. |
| Independent MAD-X/Bmad comparison | Not run | Must not be represented as completed. |

Files:

- `Accelerator_Symplectic_Validation_Report.json` — browser smoke test and in-app validation results.
- `Accelerator_Symplectic_Convergence.json` — 20/10/5 mm resolution study.

## Model scope and limitations

- This is a **canonical paraxial element-map simulator**, not a general 3D relativistic Lorentz-force tracker.
- Quadrupole propagation uses a fourth-order Yoshida split map. Dipoles and solenoids use analytic paraxial maps; sextupole/octupole and RF elements use Hamiltonian kicks.
- The RF cavity is a distributed integrated-voltage surrogate. It is not a field-solved finite-gap RF cavity with validated transit-time factors.
- Imported 3D E/B maps can be parsed/interpolated for inspection but are not currently used to advance particles in the canonical paraxial tracker.
- Radiation damping, stochastic recoil, space charge, wakefields, collective effects, and full measured fringe-field maps are not active in this Hamiltonian mode.
- Symplecticity is a structural/numerical property, not a guarantee that the selected Hamiltonian or lattice matches a real accelerator. The in-app map checks use the code's own conventions.
- The convergence test checks numerical consistency of this implementation; it is not a validation against experimental data or an independent tracking code.

## Recommended next research validation

1. Export a precise lattice definition and coordinate convention.
2. Reproduce that lattice in MAD-X or Bmad.
3. Compare tunes, Twiss functions, dispersion, chromaticity, closed orbit, and nonlinear tune shifts.
4. Repeat convergence tests for nonlinear amplitudes and momentum offsets, not only the on-axis linearization.
5. Only then interpret dynamic-aperture scans as quantitative predictions.
