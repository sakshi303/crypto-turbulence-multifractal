# Turbulence in Markets: Multifractal Scaling in Crypto Returns

An empirical test of the turbulence analogy in finance: do high-frequency returns show the multifractal cascade structure seen in fluid turbulence, and does that structure improve volatility forecasts?

> **Status:** work in progress. Sections marked `TODO` will be filled in as results come in.

---

## Motivation

In 1941 Kolmogorov predicted that velocity increments in turbulent flow scale as a simple power law with exponent ζ(p) = p/3. Real turbulence deviates from this: it is *intermittent*, and ζ(p) is concave in p. Models such as She-Leveque describe this through a multiplicative energy cascade.

Since the 1990s, researchers (Mantegna & Stanley 1996; Ghashghaie et al. 1996; Bacry, Delour & Muzy 2001) have argued that financial returns show similar structure: volatility clusters, and return moments scale across timescales in a way that looks like a cascade. The analogy has also been criticised as weaker than first claimed.

This project asks two concrete questions:

1. **Descriptive:** Is ζ(p) for high-frequency crypto returns concave, and what is the intermittency coefficient λ²?
2. **Predictive:** Does a Multifractal Random Walk (MRW) volatility forecast beat the standard HAR-RV baseline out-of-sample?

A careful negative or mixed answer is a perfectly good outcome.

## Methods

### Data
- Historical trades and klines for BTCUSDT and ETHUSDT from [Binance public data](https://data.binance.vision).
- Crypto trades 24/7, which avoids overnight gaps and makes scaling analysis cleaner than equities.
- Timescales from seconds to days.

### Pipeline
1. **Preprocessing:** clean trades, resample to regular grids, compute log returns, remove intraday seasonality (volume and volatility profiles) so it isn't mistaken for scaling.
2. **Structure functions:** compute `S_p(τ) = ⟨|r_τ|^p⟩` over a grid of τ and moments p; fit ζ(p) over a stated scaling range with bootstrapped uncertainties. Extended self-similarity (ESS, plotting `S_p` against `S_3`) is used for cleaner exponents.
3. **Intermittency coefficient:** estimate λ² from the log-volatility covariance, `Cov(ln σ_t, ln σ_{t+τ}) ≈ −λ² ln(τ / T)`.
4. **Forecasting:** MRW-based realised volatility forecasts vs. HAR-RV, evaluated out-of-sample (rolling window) with QLIKE and MSE losses and Diebold-Mariano tests.
5. **Regime analysis (optional):** rolling λ² and ζ(p) through time, e.g. around crashes.

## Results

`TODO`: fill in once the analysis is run.

| Quantity | BTCUSDT | ETHUSDT | Kolmogorov (K41) |
|---|---|---|---|
| ζ(2) | TODO | TODO | 0.667 |
| ζ(4) | TODO | TODO | 1.333 |
| λ² | TODO | TODO | n/a |

Planned figures:
- Log-log structure functions with the fitted scaling range marked
- ζ(p) vs. p against K41 and She-Leveque curves
- Log-volatility covariance vs. ln(τ)
- Out-of-sample forecast loss: MRW vs. HAR-RV
- Rolling λ² through time

## Caveats and known limitations

- **Microstructure noise** and tick discretisation contaminate the shortest timescales; τ_min is chosen and justified explicitly.
- **Scaling-range selection** is the main source of researcher degrees of freedom. Results are reported for several ranges, not just the best-looking one.
- **The analogy is a modelling choice, not a derivation.** Markets have no conserved momentum and no natural counterpart to viscosity. Scaling similarity does not imply the same mechanism.
- Crypto may not generalise to equities or FX.

## Repository structure

```
.
├── data/                 # raw and processed data (not tracked; see download script)
├── notebooks/            # exploratory analysis and figures
├── src/
│   ├── data.py           # download and cleaning
│   ├── seasonality.py    # intraday deseasonalisation
│   ├── structure.py      # structure functions, ζ(p), ESS
│   ├── mrw.py            # MRW estimation and simulation
│   ├── forecast.py       # MRW and HAR-RV forecasting, evaluation
│   └── plots.py
├── tests/                # sanity checks on synthetic data (e.g. Brownian motion, simulated MRW)
├── requirements.txt
└── README.md
```

*(Layout is the plan; adjust to match the repo as it develops.)*

## Getting started

```bash
git clone https://github.com/<your-username>/<repo-name>.git
cd <repo-name>
pip install -r requirements.txt

# download data
python -m src.data --symbol BTCUSDT --start 2023-01-01 --end 2024-12-31

# run the analysis
python -m src.structure
python -m src.forecast
```

Dependencies: Python 3.10+, NumPy, pandas, SciPy, statsmodels, Matplotlib.

## Validation

Estimators are tested on synthetic data with known answers:
- **Brownian motion:** should recover ζ(p) = p/2 and λ² ≈ 0.
- **Simulated MRW with known λ²:** should recover the input λ² within error.

Without these checks, a concave ζ(p) could just be an estimator artefact.

## References

- Kolmogorov, A. N. (1941). The local structure of turbulence in incompressible viscous fluid for very large Reynolds numbers.
- She, Z.-S. & Leveque, E. (1994). Universal scaling laws in fully developed turbulence. *Phys. Rev. Lett.* 72, 336.
- Benzi, R. et al. (1993). Extended self-similarity in turbulent flows. *Phys. Rev. E* 48, R29.
- Mantegna, R. N. & Stanley, H. E. (1996). Turbulence and financial markets. *Nature* 383, 587.
- Ghashghaie, S. et al. (1996). Turbulent cascades in foreign exchange markets. *Nature* 381, 767.
- Bacry, E., Delour, J. & Muzy, J.-F. (2001). Multifractal random walk. *Phys. Rev. E* 64, 026103.
- Corsi, F. (2009). A simple approximate long-memory model of realized volatility. *J. Financial Econometrics* 7, 174.

## License

MIT (or your choice; add a `LICENSE` file).

## Disclaimer

This is a research and learning project, not financial advice.
