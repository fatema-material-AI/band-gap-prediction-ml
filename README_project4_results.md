# Project 4 — Optical Band Gaps and Band-Edge Screening of Ta-Based Photocatalyst Supports

Diffuse-reflectance UV-Vis analysis of materials synthesized during my PhD research
on photocatalytic nitrate reduction, followed by an empirical band-edge screening
of the support candidates. All band gaps are extracted from my own raw instrument
files (`.ABS`, 191–1080 nm) by a pipeline built and validated in this project.

---

## Layer 1 — Experimental: Tauc band gaps from raw diffuse reflectance

**Pipeline:** file discovery (no hardcoded names) → header-safe loader →
sub-band-gap baseline correction → Kubelka–Munk transform F(R) = (1−R)²/2R →
Tauc plot (F·hν)^(1/r) → linear fit at the inflection point of the edge →
extrapolation to y = 0 → physical sanity guard.

**Results (condition-level, sane fits only):**

| Material | Conditions | E_g (eV) | Tauc exponent |
|---|---|---|---|
| Ta₃N₅ | 4 | **2.01 – 2.13** | direct (r = ½) |
| TaON | 6 | **2.50 – 2.68** | direct (r = ½) |
| ZnO | 2 | **3.25** (±0.002 between replicates) | direct (r = ½) |
| TiO₂ (anatase) | 1 | **3.25** | **indirect (r = 2)** |
| Ni₂P–TaON, ZnO–Ni₂P, TiO₂–Ni₂P | — | see Finding 3 | — |

### Finding 1 — Nitridation narrows the gap by ~0.4–0.5 eV
Every Ta₃N₅ condition (2.01–2.13 eV) sits below every TaON condition
(2.50–2.68 eV), with no overlap. This is the expected effect of N-for-O
substitution: N 2p states raise the valence band maximum, shrinking the gap and
extending absorption into the visible. The separation is robust even though
individual fits are noisy (see Limitations).

### Finding 2 — Anatase TiO₂ requires the indirect Tauc exponent
Fitting the pure TiO₂ spectrum with the direct exponent gives 3.46 eV
(the direct transition); the indirect exponent gives **3.246 eV (R² = 0.998)**,
matching the anatase indirect gap. This was the only material in the set where
the default direct-gap assumption fails — a concrete demonstration that the
Tauc exponent is a physical hypothesis to be tested, not a convention.

### Finding 3 — Ni₂P is a metallic cocatalyst, not a dopant
Directly measured pure Ni₂P shows broad featureless absorption across the entire
visible/NIR with a rising tail from 800 → 1000 nm (free-carrier behavior) and no
absorption edge; the fitting guard correctly refuses to assign it a band gap.
In the composites this appears as a strong sub-band-gap background that degrades
Tauc linearity (Ni₂P–TaON fits: R² 0.09–0.36). Where a gap is recoverable, it
tracks the bare host (ZnO–Ni₂P: 3.24 vs ZnO 3.25 eV), confirming Ni₂P sits on
the surface as a separate metallic phase and does not perturb the host band
structure. The TiO₂–Ni₂P edge reads 0.12–0.17 eV above bare TiO₂ — a measurable
artifact of the metallic background tilting the baseline, not a real shift.

### Methodological guards that mattered
- **Fit at the inflection point, not "wherever it looks straight."** A naive
  R²×slope window selection drifted above the edge and inflated every gap;
  switching to argmax(dy/dE) reduced replicate scatter up to 19×.
- **R² is not a sufficient quality metric.** One spectrum produced R² = 0.9999
  with E_g = 1.80 eV — a perfect fit to the wrong slope, requiring a 1.7 eV
  backward extrapolation. A physical guard (extrapolation ≤ 0.5 eV below the
  fit window) rejects this class of error; statistics cannot.

### Limitations (stated, not hidden)
- Sample masses were small; the Kubelka–Munk optically-thick assumption is
  partially violated. Baselines drift (−0.76 to +0.96 sub-gap absorbance),
  so **ranges are reported rather than point values**, and the TaON-vs-Ta₃N₅
  *comparison* is the robust claim, not any individual number.
- Most conditions are single scans (n = 1); syn/bm conditions have n = 2–3.
- Baseline correction assumes the Ni₂P background is spectrally flat, which is
  only approximately true; the apparent +0.09 eV shift of Ni₂P–TaON vs TaON is
  attributed to this artifact.

---

## Layer 2 — Computational: band-edge alignment vs the nitrate reduction potential

Band gap alone determines light harvesting, not catalytic ability: the
conduction band minimum (CBM) must sit above (more negative than) the reduction
potential for the excited electron to drive the reaction. Band edges were
estimated with the Xu–Schoonen / Butler–Ginley relation:

    CBM = X − 4.5 − ½·E_g        (X = geometric mean of atomic Mulliken
    VBM = CBM + E_g               electronegativities; potentials vs NHE)

**Calibration first:** the method reproduces the known oxide CBMs
(TiO₂: −0.29 V calc vs ≈ −0.29 V lit; ZnO: −0.33 V vs ≈ −0.31 V), validating
the constants before any conclusion is drawn about the nitrides.

**Inputs:** all four support gaps are my own measurements
(TiO₂ 3.25, ZnO 3.25, TaON 2.50, Ta₃N₅ 2.07 eV).
Reference potential: NO₃⁻ reduction at **[YOUR VALUE] V vs NHE**
([your pathway], pH [your pH]).

**The trade-off the diagram shows:** the oxides pair strong reducing power
(CBM ≈ −0.3 V) with UV-only absorption; the nitrides gain visible-light
absorption but their CBM drops ~0.6 V toward the reduction potential. Narrowing
the gap buys photons at the cost of driving force — the central design tension
in support selection for visible-light nitrate reduction.

**Caveat:** the Xu–Schoonen scheme is parameterized for oxides and is least
reliable for (oxy)nitrides; literature places the TaON/Ta₃N₅ CBM more negative
(≈ −0.3 V) than this estimate, which would strengthen, not weaken, the
visible-light case. Values for the nitrides are screening estimates, calibrated
against the oxide anchors.

---

## Files
| File | Contents |
|---|---|
| `TaON_Optical_Analysis.ipynb` | Full pipeline: discovery → Tauc → diagnostics → band-edge diagram |
| `band_gaps.csv` | Per-spectrum E_g, R², fit window, quality flag |

## Reproducibility
Every output in the notebook was produced by a single Restart-and-run-all on
the complete dataset (execution counts 1…7). Filenames are discovered from
disk, never hardcoded; per-family Tauc exponents and fit windows are declared
in one place (`R_EXP`, `SEARCH`); unphysical fits are rejected and reported,
not dropped.
