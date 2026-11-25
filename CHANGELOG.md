# Changelog

## v1.0.0

- Published a read-only Vercel portfolio with 144 simulated assessments scored by the frozen XGBoost model, asset history, alert investigations, precomputed SHAP contributions and waterfalls, and Model Health comparisons.
- Added recorded telemetry replay and four scripted Copilot examples, including evidence citations and an abstention example.
- Added frozen artifact verification, GitHub Actions validation, and a separate stateless CPU inference container for optional use. The public deployment uses recorded scores.
- Published the case study, deployment guide, benchmark methods, and dataset attribution in the clean publication repository.

The synthetic AI4I holdout yielded 0.8655 average precision. The telemetry and browser performance figures come from local benchmarks. Live-provider Copilot evaluation and real-equipment generalization remain open.

[Live demo](https://fleet-mind-portfolio.vercel.app/) · [Release validation](docs/release-validation.md) · [Case study](docs/case-study.md)
