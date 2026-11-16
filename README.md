# FreshCart Data Engineering Pipelines: From Git to Gold

- **Name:** Vikas Choudhary
- **Roll Number:** 26pcso06
- **GitHub Repository:** [https://github.com/vikas-choudhary-26pcso06/freshcart-pipelines](https://github.com/vikas-choudhary-26pcso06/freshcart-pipelines)

End-to-end modern data engineering platform demonstrating version control, ELT data warehousing on Snowflake, orchestrated machine learning pipelines with Mage, and real-time event streaming using RabbitMQ topic exchanges.

---

## 📁 Repository Structure

```
freshcart-pipelines/
├── README.md                           # Project overview, architecture, and documentation
├── .gitignore                          # Ignored patterns (.env, *.pkl, *.csv, mage.log, __pycache__/)
├── requirements.txt                    # Project Python dependencies
├── elt/                                # Part B: FreshCart ELT Pipeline on Snowflake
│   └── freshcart_elt.ipynb             # Interactive Colab/Jupyter notebook with outputs
├── iris_project/                       # Part C: Orchestrated ML Pipeline with Mage
│   ├── data_loaders/
│   │   └── load_iris.py                # Load raw Iris dataset with 150-row test gate
│   ├── transformers/
│   │   ├── data_validation.py          # Custom block: Checks nulls and 3 target classes
│   │   ├── prepare_data.py             # 80/20 train/test split with quality test
│   │   ├── train_model.py              # Decision tree classifier with 80% accuracy gate
│   │   └── evaluate_model.py           # Evaluation branch: Confusion matrix & macro metrics
│   ├── data_exporters/
│   │   └── save_results.py             # Downstream exporter writing iris_predictions.csv
│   └── pipelines/iris_classifier/
│       └── metadata.yaml               # Directed Acyclic Graph (DAG) configuration
├── stream/                             # Part D: Real-Time Event Streaming with RabbitMQ
│   ├── dispatcher.py                   # Topic publisher generating city.zone.type events
│   ├── driver.py                       # Driver consumer with ACK/NACK validation logic
│   ├── simulate_stream.py              # In-process topic exchange and consumer fleet simulator
│   └── lakehouse_consumer.py           # Bonus: Streaming micro-batch consumer into Lakehouse
├── tests/
│   └── test_prepare_data.py            # CI unit test validating 80/20 partition logic
├── .github/workflows/
│   └── ci.yml                          # GitHub Actions CI workflow running Python 3.12 & pytest
```

---

## 🚀 Quickstart Guide

### 1. Environment Setup
```bash
# Clone the repository
git clone https://github.com/d2e-iiitdm/freshcart-pipelines.git
cd freshcart-pipelines

# Create and activate virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

### 2. Running Unit Tests
```bash
pytest -v
```

### 3. Running the Mage ML Pipeline
```bash
# Run pipeline from command line with test execution
mage run iris_project iris_classifier --test

# Pass custom runtime parameters (e.g., max_depth)
mage run iris_project iris_classifier --runtime-vars '{"max_depth": 3}' --test
```

### 4. Running the Real-Time Event Stream (RabbitMQ)
```bash
# Option A: In-Process Fleet Simulator (Zero Setup / No external server needed)
python stream/simulate_stream.py

# Option B: Live RabbitMQ (if RABBITMQ_URL is configured)
# Terminal 1: Launch Dispatcher
python stream/dispatcher.py

# Terminal 2: Driver Ravi (Koramangala any)
python stream/driver.py Ravi "bangalore.koramangala.*"

# Terminal 3: Driver Priya (Indiranagar any)
python stream/driver.py Priya "bangalore.indiranagar.*"

# Terminal 4: Supervisor Control (All Bangalore events)
python stream/driver.py Control "bangalore.#"
```

### 5. Running the Lakehouse Stream Ingestion (Bonus)
```bash
python stream/lakehouse_consumer.py
```

---

## 📊 Summary of Assignment Parts

- **Part A (Git & CI/CD)**: Four-box Git architecture, git command lifecycle, automated GitHub Actions workflow verifying test gates on push.
- **Part B (FreshCart ELT on Snowflake)**: Medallion architecture (Bronze raw VARIANT → Silver cleaned/deduplicated → Gold business metrics), SCD Type 2 dimension tracking, currency conversion, quarantine error handling, and strict idempotency via `row_hash` + `MERGE`.
- **Part C (Mage ML Pipeline)**: Modular ML pipeline with block-level quality test gates. Demonstrates test-gating where failing assertions (e.g. at `max_depth=1`) abort downstream file exports (`save_results`).
- **Part D (RabbitMQ Event Stream)**: Topic exchange (`rapido-dispatch`) with routing keys (`city.zone.type`) and binding patterns (`*`, `#`). Implements consumer ACK/NACK handling to discard poison pill payloads (`requeue=False`).
- **Bonus**: Real-time micro-batch ingestion into Snowflake Lakehouse with verified idempotency.
