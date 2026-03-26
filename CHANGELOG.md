# Changelog

All notable changes to this project will be documented in this file.

The format follows [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

---

## [1.1.0] - 2026-03-25

### Added
- `opportunity-detector` skill for intraday anomaly detection and noise filtering
- `market-intel` skill for bilingual (English + Chinese) real-time news intelligence
- Call Ratio Spread added to trade-analyst permitted strategy table (aligning with quant-analyst)
- Financial disclaimer added to README

### Changed
- `plugin.json`: Fixed `defaults.LANGUAGE` from `"en"` to `"English"` to match configuration options
- `README.md`: Corrected assertion count from 35 to 28
- `macro-analyst/SKILL.md`: Removed duplicate GLD entry; merged into single line
- `macro-analyst/SKILL.md`: Fully translated to English for marketplace submission
- `examples/portfolio_data_template.json`: Fixed backslash path to forward slash

---

## [1.0.0] - 2026-03-01

### Added
- Initial release with 5 core analyst skills: `trade-analyst`, `macro-analyst`, `sector-analyst`, `quant-analyst`, `event-analyst`
- Multi-scenario equilibrium pricing framework (GTMVF)
- VIX-adjusted Kelly position sizing
- Bilingual news intelligence support (English + Chinese)
- Devil's Advocate and contradiction detection in analyst synthesis
- 3 pre-built eval scenarios for trade-analyst with 28 expectations
