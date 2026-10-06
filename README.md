# Project 1: Upsilon Resonances in the CMS Dimuon Spectrum

This is the starter repository for PHYS690 Project 1. The project is intentionally open-ended: use a public data set and a defensible statistical model to extract a physical parameter, compare at least two reasonable modeling choices, and explain what your uncertainty statement does--and does not--include.

The concrete example below uses open CMS dimuon data from the CERN Open Data Portal. You will analyze the invariant-mass spectrum of opposite-sign muon pairs in the Upsilon region, model three nearby resonances sitting on a smooth background, and extract peak positions, detector-resolution-like widths, and yields with defensible uncertainties. This is a teaching analysis, not a precision particle-physics measurement.

## Repository Layout

```text
.
project1-nkoval-wm/
├── .github/
├── .vscode/
├── data/
├── figures/
│   ├── Bootstrap Analysis.png
│   ├── fit_troubleshooting.png
│   ├── Four_Peak_Mass_Fit.png
│   ├── Likelihood_Intensity_Model.png
│   ├── Normalized_Residual.png
│   └── Profile_Likelihood_Signal_Yields.png
├── notebooks/
│   ├── Four_Peak_model.ipynb
│   └── load_and_compute_mass.ipynb
├── venvp1/
├── .gitignore
├── README.md
├── requirements.txt
└── Results.csv
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

Using public CMS dimuon events, we set out to estimate the masses and relative yields of the Upsilon(1S), Upsilon(2S), and Upsilon(3S) states from the invariant-mass distribution between about 8 and 12 GeV. Further, we investigate the presence of a fourth Upsilon(4S) peak.

## Data

Use the CMS Open Data record for derived 2011 Upsilon-to-dimuon candidate events:

```text
https://opendata.cern.ch/record/cms-5206
```

The parent record describing related simplified Run211A lepton data sets is:

```text
https://opendata.cern.ch/record/cms-545
```

The Upsilon sample contains 20,000 events selected from DoubleMu 2011 with two muons, opposite-sign charge, at least one global muon, both muons within `|eta| < 2.4`, and invariant mass between 8 and 12 GeV.

Do not commit the downloaded table, generated scratch output, or `.venv/`. Record the URL, download date, and any filtering or column transformations in your analysis. Inspect the table rather than assuming that every column is needed. The starter notebook in `notebooks/` shows how to compute the invariant mass from `E1`, `px1`, `py1`, `pz1`, `E2`, `px2`, `py2`, and `pz2`; check the units and explain the mass window and event selections you use.

```markdown

## Results

The data and following analysis support a model consisting of three Upsilon resonances on top of a polynomial Chebyshev background. The best-fit model gives signal yields and mass locations of

| Resonance | Fitted Mass (GeV) | Signal Yield |
| --- | ---: | ---: |
| $\Upsilon(1S)$ | 9.4460 | 2238 |
| $\Upsilon(2S)$ | 10.014 | 825 |
| $\Upsilon(3S)$ | 10.346 | 558 |


The reduced chi2, p-value, and likelihood-ratio comparisons support the use of the three-peak model. In particular, the signal-plus-background model provides a substantial improvement over a background-only model, while the addition of a fourth peak produces only a nominal improvement in likelihood despite adding three additional parameters.


## Analysis

In this project we develop a model for the Upsilon resonances in the CMS dimuon spectrum. We evaluate the 8-12 GeV mass range and model the data using three Gaussian peaks on top of a second-order Chebyshev polynomial background. The initial guesses for the three peak locations are taken from the Particle Data Group values for the \(\Upsilon(1S)\), \(\Upsilon(2S)\), and \(\Upsilon(3S)\) resonances.

Our full model is

\[
I(m)
=
N_1G_1(m)
+
N_2G_2(m)
+
N_3G_3(m)
+
N_bB(m),
\]

where \(N_1\), \(N_2\), and \(N_3\) are the signal yields of the three Upsilon peaks, \(N_b\) is the background yield, and the \(G_i(m)\) are Gaussian distributions. The background is modeled using a second-order Chebyshev polynomial,

\[
B(m)
\propto
T_0(x)+c_1T_1(x)+c_2T_2(x),
\]

where the 8-12 GeV mass range is mapped onto the standard Chebyshev domain \([-1,1]\). Each signal and background distribution is normalized over the selected mass range.

We fit the data using an extended unbinned Poisson likelihood. The model intensity is evaluated at the mass of each observed event and has units of events/GeV. When plotting the model against a histogram, we multiply the intensity by the histogram bin width to convert it to events/bin.

The negative log-likelihood is

\[
-\ln L
=
N_{\mathrm{model}}
-
\sum_i \ln I(m_i),
\]

where

\[
N_{\mathrm{model}}
=
N_1+N_2+N_3+N_b.
\]

This means that the fit uses both the total number of observed events and where each event occurs in the mass spectrum. We optimize the model parameters using `scipy.optimize.minimize`, with bounds and physically reasonable initial parameter guesses.

### Goodness of Fit

After fitting the model, we calculate normalized residuals,

\[
r_i
=
\frac{n_i-\nu_i}{\sqrt{\nu_i}},
\]

where \(n_i\) is the observed number of events in a bin and \(\nu_i\) is the number predicted by the model.

We then calculate the Pearson chi-square statistic,

\[
\chi^2
=
\sum_i
\frac{(n_i-\nu_i)^2}{\nu_i},
\]

along with the reduced chi-square and corresponding p-value. These quantities are used as a binned goodness-of-fit check on the unbinned likelihood model.

The reduced chi2 value near one supports the use of this model. One trait of a good fit is a chi2 value of approximatley one.

The p-value indicates that it wouldn't be uncommon for repeated trials to get similar values for the chi2. This supports the case for this fit.

The plot of normalized residuals appears to randomly distrubited around zero in an even fashion. This shows no systematic change in the way the model predicts the number of events over the mass range.

### Signal Plus Background vs. Background Only

We compare the signal-plus-background model with a background-only model using the likelihood-ratio statistic

\[
q
=
-2\ln\frac{L_{\mathrm{background}}}{L_{\mathrm{full}}}
=
2\left[
(-\ln L)_{\mathrm{background}}
-
(-\ln L)_{\mathrm{full}}
\right].
\]

A larger value of \(q\) means that the signal-plus-background model produces a substantially better likelihood than the background-only model.

The large value of q means that there is a large difference in the least likehood fit between the model with peaks and the model with only background signal. Displaying how the signal + background improves the fit considerably from  a model with only background contributions.

a p value of zero says for a background only hypothesis. getting a chi^2 differnce this large between the background model and the signal is extrmeely improbable. This supports the supposition that the "background only" model is inadequate

Because the signal yields are bounded at zero, the usual Wilks-theorem chi-square approximation is not exact, so the likelihood-ratio p-value should be interpreted with this caveat.

### Three-Peak vs. Four-Peak Model

We also test whether adding a fourth Gaussian peak improves the description of the data. The four-peak model adds three additional parameters: a fourth signal yield, peak mean, and peak width.

The comparison uses

\[
q_{3\rightarrow4}
=
-2\ln\frac{L_{3\mathrm{\ peak}}}{L_{4\mathrm{\ peak}}}
=
2\left[
(-\ln L)_{3\mathrm{\ peak}}
-
(-\ln L)_{4\mathrm{\ peak}}
\right].
\]

We expect Upsilon(4S) to be at 10.579 GeV, which was not found by the optimizer. 

I plotted all four signals separately because I wanted to visualize where the optimizer set the fourth peak. From what we know about Upsilon(4S) this peak is not in the correct place, but we did a q value test to determine if the extra peak improved the fit.

This confirmed our expectation. The small delta q means that the likelihood imporvement from the fourth peak was nominal while adding in three axtra parameters. The realtively large p value means that assuming the three peak model was correct, obtaining this chi^2 from this data was likely. Thus supporting the case for a three peak fit as opposed to the four peak fit.

As in the background-only comparison, the additional signal yield is bounded at zero, so the standard Wilks-theorem approximation is not exact.

### Profile Likelihood

To get a sense of the uncertainty in each of the three signal yields, we calculate a profile likelihood. For each signal peak, we fix its yield to a range of values and refit all of the remaining parameters.

The profile-likelihood statistic is

\[
q(\mu)
=
-2\ln
\frac{L(\mu,\hat{\hat{\eta}}_\mu)}
{L(\hat{\mu},\hat{\eta})}
=
2\left[
-\ln L(\mu,\hat{\hat{\eta}}_\mu)
+
\ln L(\hat{\mu},\hat{\eta})
\right].
\]

For one parameter of interest, the approximate 68% confidence interval is found where

\[
\Delta q
=
q(\mu)-q(\hat{\mu})
=
1.
\]

This analysis produces the one standard deviation confidence interval around each of the signal yield parameters.

It is work noting that the uncertainties are all between 40-100. However, the estimated count for the first peak is roughly 2.5x and 4x the estimate for the second and third peaks respectively, so our percent uncertainty for the estimate of the second and third peak is larger.

We repeat this process to determine a confidence interval around the mass means. 

### Parametric Bootstrap

We also perform a parametric bootstrap as an independent check on the uncertainty of the fitted parameters. Using the parameters from the best fit, we generate fictitious datasets from the fitted model.

Each simulated event is then assigned to one of the three signal peaks or the background according to the fitted component yields. Signal masses are sampled from the corresponding Gaussian distribution and background masses are sampled from the fitted Chebyshev distribution. Each fictitious dataset is then fit using the same extended unbinned likelihood used on the original data.

We perform the parametric bootstrap as an estimate to the uncertainty present in each of the signal parameters. The method works by generating ficticious but plausible data sets. Our model is fitted to the ficticious data set and we evaluate the spread of our best fit parameters.

It's straightforward to see that in the majority of ficticous datasets, our model chooses the same parameters we had deterimed from the given data set. Some spread is present, however, particulalry in terms of the signal means, the value are spread over relatively small regions around the best fit signal.

### Sensitivity of Analysis

It is worth noting that the measurement is sensitive to changes in the selected mass range. For instance, reducing the range from \(8.0-12.0\) GeV to \(9.0-11.0\) GeV causes fluctuations in the fitted mass means on the order of \(10^{-2}\) GeV. By comparison, aside from the upper \(1\sigma\) uncertainty on the \(\Upsilon(1S)\) peak, the 68% confidence intervals from the profile likelihood are on the order of \(10^{-3}-10^{-4}\) GeV. This indicates that the fitted mass means are more sensitive to the choice of mass window than would be suggested by the statistical uncertainty alone.



## References

- Most of this code was direclty adapted from Justin Stevens' PHYS 690 interactive lectures. Particularly lectures seven and eight.
- The background model utilized was explained to me by Ben Curcio. Ben used this background model during his research with the GlueX group at Jefferson Laboratory. 
- Generative AI was used via the VS code "Codex" extesnion. The nature of the usage was primarily to address syntax errors as I adapted the lecture 8 code from one gaussian to three and from an exponential to Chebychev background. 
    - The exception being the bootstrap analysis section. The general structure of this section was developed by me (adapted from Justin Stevens). However, I struggled to produce a meaningful final product. After some time of attempting to solve it myself, I described the situation to ChatGPT, and utilized the suggestions. Explicitly, these suggestions include recognizing that the proposed random chebychev background should be larger than the model function and recognizing that the fake sample point belonged, with some probability to one of the peaks or the background, so we had to choose which signal to attribute the fake mass to.
    - AI was used to translate the equations in the results/analysis sections to Latex as well as for spelling/grammar corrections. 



