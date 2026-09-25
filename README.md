# Project 1: Upsilon Resonances in the CMS Dimuon Spectrum

This is the starter repository for PHYS690 Project 1. The project is intentionally open-ended: use a public data set and a defensible statistical model to extract a physical parameter, compare at least two reasonable modeling choices, and explain what your uncertainty statement does--and does not--include.

The concrete example below uses open CMS dimuon data from the CERN Open Data Portal. You will analyze the invariant-mass spectrum of opposite-sign muon pairs in the Upsilon region, model three nearby resonances sitting on a smooth background, and extract peak positions, detector-resolution-like widths, and yields with defensible uncertainties. This is a teaching analysis, not a precision particle-physics measurement.

## Repository Layout

```text
.
├── .gitignore
├── README.md
├── requirements.txt
├── data/
│   └── raw/
│       └──  (downloaded data; do not commit)
├── figures/
│   └── .gitkeep
├── notebooks/
│   └── load_and_compute_mass.ipynb
└── scripts/
    └──  (optional reproducibility script)
```

## Setup

Open this repository folder in VS Code and use `Terminal > New Terminal`.

On macOS:

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
```

On Windows Git Bash:

```bash
python -m venv .venv
source .venv/Scripts/activate
python -m pip install -r requirements.txt
```

After installing packages, open `notebooks/load_and_compute_mass.ipynb` and select the `.venv` Python kernel. This notebook shows how to load the CMS CSV and compute the dimuon invariant mass from the input four-vectors. It is only a starting point, not a complete project submission. You may extend it or create your own analysis notebook or script for the project fit.

## Scientific question

Using public CMS dimuon events, estimate the masses and relative yields of the Upsilon(1S), Upsilon(2S), and Upsilon(3S) states from the invariant-mass distribution between about 8 and 12 GeV. Ask whether the data in your chosen mass window support three distinct resonance components over a smooth background, and test whether adding a fourth Upsilon-like peak near the Upsilon(4S) mass is required by the data. Explain how strongly your conclusion depends on assumptions about peak widths, background shape, and binning.

The natural widths of these states are only tens of keV, much smaller than the detector mass resolution in this simplified analysis. Treat fitted Gaussian widths as effective detector/analysis resolution parameters, not measurements of the intrinsic Upsilon widths.

## Data

Use the CMS Open Data record for derived 2011 Upsilon-to-dimuon candidate events:

```text
https://opendata.cern.ch/record/cms-5206
```

The parent record describing related simplified Run2011A lepton data sets is:

```text
https://opendata.cern.ch/record/cms-545
```

The Upsilon sample contains 20,000 events selected from DoubleMu 2011 with two muons, opposite-sign charge, at least one global muon, both muons within `|eta| < 2.4`, and invariant mass between 8 and 12 GeV.

Do not commit the downloaded table, generated scratch output, or `.venv/`. Record the URL, download date, and any filtering or column transformations in your analysis. Inspect the table rather than assuming that every column is needed. The starter notebook in `notebooks/` shows how to compute the invariant mass from `E1`, `px1`, `py1`, `pz1`, `E2`, `px2`, `py2`, and `pz2`; check the units and explain the mass window and event selections you use.

## Suggested direction

Develop an analysis chain that includes, at minimum:

- an inspection and visualization of the data with uncertainties;
- a clearly defined smooth-background model for the selected mass window;
- a signal-plus-background model with three nearby Upsilon peaks, such as three Gaussian peaks on a linear, polynomial, or exponential background;
- a binned Poisson likelihood or a weighted least-squares fit to histogram counts, with sensible parameter initialization and bounds where needed;
- residual or pull diagnostics that are appropriate for counting data;
- a goodness-of-fit assessment and a model-comparison argument that does not rely only on reduced chi-square;
- a hypothesis test or likelihood-ratio comparison of a three-peak model against a four-peak model, with a clear statement of caveats;
- a parameter uncertainty statement, including the fit covariance or curvature estimate and one resampling-based check such as a bootstrap or jackknife;
- a short discussion of sensitivity to at least one analysis choice, such as bin width, mass window, background form, shared versus independent peak widths, fixed versus floating peak separations, or starting point.

You may use `scipy.optimize.curve_fit`, `scipy.optimize.least_squares`, or a likelihood-based optimizer. If you use weighted least squares, justify the binning and the count uncertainties. If you use a Poisson likelihood, state the likelihood clearly and explain how you estimate parameter uncertainties. Keep units explicit throughout.

A reasonable starting point is the full 8--12 GeV Upsilon sample. A minimal signal model might include three Gaussian normalizations, one common width, one overall mass offset or scale, and background parameters. For the fourth-peak test, a conservative approach is to add one nonnegative peak normalization near the PDG Upsilon(4S) mass while keeping the mass offset and resolution treatment consistent with the three-peak model. A stronger analysis might compare this to a model with independent widths, fixed PDG mass splittings, or a different background shape. Be careful: allowing every peak position and width to float independently may make the fit less stable than it first appears.

For the three-versus-four peak comparison, do not report a p-value mechanically. The additional signal yield is bounded below by zero, and any search over possible fourth-peak locations introduces a look-elsewhere effect. A likelihood-ratio statistic is still useful, but your interpretation should state whether the fourth-peak mass was fixed in advance and what approximation you used.

The lectures provide the relevant tools: weighted least squares and covariance (Lecture 4), diagnosing fit failures and weak identification (Lecture 5), minimizers and derivatives (Lecture 6), goodness of fit and nested-model comparison (Lecture 7), and bootstrap/jackknife uncertainty (Lecture 8). You are not expected to reproduce every technique. Choose the methods that answer your question credibly.

## Reference Values and Example

Use the Particle Data Group values as external reference points, not as results you are trying to force the fit to reproduce:

| State | PDG mass | PDG natural width |
| --- | ---: | ---: |
| Upsilon(1S) | 9460.40 +/- 0.10 MeV | 54.02 +/- 1.25 keV |
| Upsilon(2S) | 10023.4 +/- 0.5 MeV | 31.98 +/- 2.63 keV |
| Upsilon(3S) | 10355.1 +/- 0.5 MeV | 20.32 +/- 1.85 keV |
| Upsilon(4S) | 10579.4 +/- 1.2 MeV | 20.5 +/- 2.5 MeV |

For an example of how CMS presents a professional Upsilon mass-spectrum fit in the same dimuon final state, see Figure 1-a from CMS-BPH-12-006:

![CMS Upsilon dimuon mass fit example](https://cms-results.web.cern.ch/cms-results/public-results/publications/BPH-12-006/CMS-BPH-12-006_Figure_001-a.png)

PDF version of the figure: https://cms-results.web.cern.ch/cms-results/public-results/publications/BPH-12-006/CMS-BPH-12-006_Figure_001-a.pdf

That figure comes from a full CMS publication with a much more complete analysis chain than this project, not from the simplified education CSV directly. Use it as a visual benchmark for what a fit, component model, and pull distribution can look like, not as a template to copy exactly.

Suggested references:

- CMS Open Data, "Y to two muons from 2011": https://opendata.cern.ch/record/cms-5206
- CMS Open Data, "Datasets derived from the Run2011A SingleElectron, SingleMu, DoubleElectron, and DoubleMu primary datasets": https://opendata.cern.ch/record/cms-545
- Particle Data Group, Upsilon(1S): https://pdglive.lbl.gov/Particle.action?node=M049
- Particle Data Group, Upsilon(2S): https://pdgprod.lbl.gov/pdgprod/pdgLive/Particle.action?node=M052
- Particle Data Group, Upsilon(3S): https://pdglive.lbl.gov/Particle.action?node=M048
- Particle Data Group, Upsilon(4S): https://pdgprod.lbl.gov/pdgprod/pdgLive/Particle.action?home=sumtabM&node=M047
- CMS Collaboration, "Measurements of the Upsilon(1S), Upsilon(2S), and Upsilon(3S) differential cross sections in pp collisions at sqrt(s) = 7 TeV," Phys. Lett. B 749 (2015) 14: https://cms-results.web.cern.ch/cms-results/public-results/publications/BPH-12-006/

## Deliverable

Submit a concise, reproducible analysis in a notebook, script, or combination of both. It should allow another student to download the public data and regenerate your main results. Your final work should go beyond the starter notebook by fitting models, comparing them, and making an uncertainty statement. Include:

- a brief statement of the scientific question and data provenance;
- the model equations, fitted parameters, units, and uncertainty method;
- one figure showing the data, fitted models, and useful residual information;
- a compact results table;
- your model-comparison result, including the three-versus-four peak test and its limitations;
- a paragraph distinguishing statistical uncertainty from unmodeled systematics and analysis sensitivity.

Save final figures in `figures/` and keep exploratory files out of the repository. Your conclusion should report what the data support, not simply which optimizer returned the smallest objective. It is acceptable if your exact fitted values differ from another student's values, provided your modeling choices are documented and defensible.

```text
figures/upsilon_resonance_fit.png
```

You should also update this `README.md` with the exact command(s) needed to rerun your analysis.

## Submission

This repository should remain private inside the `WM-PHYS690-Fall2026` GitHub organization. Make meaningful commits as you work, then push your final work.

Finally, submit a pull request from the `submission` branch into `main` in your private project repository. This will inform Prof. Stevens that your submission is ready to be evaluated.
