# Trust-Score-Calculation
Coding Trust Score Computation for Quantum-Blockchain

quanfraud-trustscore/
├── LICENSE (MIT)
├── README.md  ← setup & commands
├── requirements.txt
├── src/quanfraud/
│   ├── __init__.py
│   ├── validators.py          # GHZ/θ, DID, kernel-privacy, audit, calibration checks
│   ├── trust_score.py         # 0–5 Trust Score calculator
│   ├── realtime.py            # streaming simulator (22 s, BA≈0.42) + scoring
│   ├── plotting.py            # dual x-axis (Time bottom, BA top)
│   └── schemas.py             # data model (optional)
├── scripts/
│   ├── simulate_stream.py     # generate CSV/JSON log + figure
│   ├── eval_offline.py        # validate from JSON + recompute trust
│   └── make_plots.py          # plot from CSV/JSON logs
├── data/
│   ├── example_batches.json   # offline sample
│   └── example_stream_log.csv # small CSV sample
└── tests/
    └── test_trust_score.py    # quick sanity tests

# 1) Create env and install deps
python -m venv .venv && source .venv/bin/activate         # Windows: .venv\Scripts\activate
pip install -r requirements.txt

# 2) Simulate realtime batches, compute Trust Score, save logs + figure
python scripts/simulate_stream.py --batches 12 \
  --out data/example_stream_log.csv \
  --json data/example_stream_log.json \
  --fig data/quanfraud_realtime_trust_vs_time_ba.jpg

# 3) Make a plot from an existing log (CSV/JSON)
python scripts/make_plots.py --log data/example_stream_log.csv \
  --out data/quanfraud_realtime_trust_vs_time_ba.jpg

# 4) Offline validation from a JSON of batches
python scripts/eval_offline.py --json data/example_batches.json \
  --out-csv data/offline_eval.csv \
  --out-fig data/offline_trust_vs_time_ba.jpg

# 5) Run unit tests
python -m pytest -q

