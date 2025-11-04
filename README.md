# 1) Trust-Score-Calculation
Coding Trust Score Computation for Quantum-Blockchain

quanfraud-trustscore/
├── LICENSE                          # MIT
├── README.md                        # setup & commands
├── requirements.txt
├── src/quanfraud/
│   ├── __init__.py
│   ├── validators.py                # GHZ/θ, DID, kernel-privacy, audit, calibration checks
│   ├── trust_score.py               # 0–5 Trust Score calculator
│   ├── realtime.py                  # streaming simulator (Time≈22 s, BA≈0.42) + scoring
│   ├── plotting.py                  # dual x-axis plot: Time (bottom), BA (top)
│   └── schemas.py                   # (optional) typed batch model
├── scripts/
│   ├── simulate_stream.py           # simulate → CSV/JSON logs + figure
│   ├── eval_offline.py              # validate from JSON, recompute trust, plot
│   └── make_plots.py                # plot from any CSV/JSON log
├── data/
│   ├── example_batches.json         # small offline sample
│   └── example_stream_log.csv       # tiny CSV sample
└── tests/
    └── test_trust_score.py          # quick sanity tests


## Windows (PowerShell) 

cd quanfraud-trustscore
python -m venv .venv; .\.venv\Scripts\Activate.ps1
pip install -U pip
pip install -r requirements.txt
pip install -e .


## 2) Quick smoke test
pytest -q


You should see all tests passing.

## 3) Generate realtime logs + figure (default: 12 batches)
python scripts/simulate_stream.py \
  --batches 12 \
  --out  data/example_stream_log.csv \
  --json data/example_stream_log.json \
  --fig  data/quanfraud_realtime_trust_vs_time_ba.jpg


Outputs

data/example_stream_log.csv (tabular batches)

data/example_stream_log.json (same as JSON)

data/quanfraud_realtime_trust_vs_time_ba.jpg (Time on bottom X-axis, BA labels on top X-axis, Trust Score on Y-axis)

## 4) Plot from any existing log

CSV or JSON both work:

python scripts/make_plots.py \
  --log data/example_stream_log.csv \
  --out data/plot_from_csv.jpg
# or
python scripts/make_plots.py \
  --log data/example_stream_log.json \
  --out data/plot_from_json.jpg

## 5) Offline validation (recompute Trust Score)
python scripts/eval_offline.py \
  --json data/example_batches.json \
  --out-csv data/offline_eval.csv \
  --out-fig data/offline_trust_vs_time_ba.jpg


This:

Recomputes Trust Score from each row’s fields

Writes a fresh CSV

Produces the dual-axis figure

## 6) Where to tweak core behavior
Trust Score (0–5)

src/quanfraud/trust_score.py + src/quanfraud/validators.py
Adds one point for each true condition:

GHZ/θ pass: ghz_fidelity ≥ threshold (default 0.50)

DID verified: did_ok = True

Kernel privacy: kernel_privacy_ok = True

Audit trail write success: audit_ok = True

Calibration: ECE ≤ threshold (default 0.08)

Simulator (runtime & BA)

src/quanfraud/realtime.py: change these means/sigmas to match your setup:

runtime_mu=22.0, runtime_sigma=1.5
ba_mu=0.42, ba_sigma=0.02
ghz_mu=0.62, ghz_sigma=0.07
ece_mu=0.05, ece_sigma=0.02

## Plotting style

src/quanfraud/plotting.py: all matplotlib (no seaborn); clean, journal-friendly defaults.

Bottom X-axis: Time (seconds)

Top X-axis: Balanced Accuracy (BA) per batch

Y-axis: Trust Score (0–5)




### For Quick start (upload to GitHub then run)
git clone https://github.com/<you>/quanfraud-trustscore-extended.git
cd quanfraud-trustscore-extended
python -m venv .venv && source .venv/bin/activate   # Windows: .venv\Scripts\activate
pip install -U pip
pip install -e .

## Simulate “live” with adapter hooks
python scripts/run_stream_live.py --config config.yaml --batches 12 \
  --out data/live_stream.csv --json data/live_stream.json \
  --fig data/live_trust_vs_time_ba.jpg


## Outputs:

data/live_stream.csv, data/live_stream.json

data/live_trust_vs_time_ba.jpg (Time bottom axis, BA top axis)

## Plug in your real signals

Open these files and replace stubs with real calls:

src/quanfraud/adapters/ghz_ibm.py → return GHZ fidelity from IBM Runtime/Qiskit.

src/quanfraud/adapters/did_hyperledger.py → call your Aries/Indy verifier.

src/quanfraud/adapters/audit_chain.py → write the on-chain receipt tuple.

## If you have probability outputs, use src/quanfraud/adapters/ece.py to compute ECE.

CI & packaging

Push the repo — GitHub Actions runs unit tests and a build.

## Build locally:

python -m pip install build
python -m build
