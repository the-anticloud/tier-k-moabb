# Deploy Guide — K_MOABB
**Tier:** TIER_7_BIOSIGNALS_NEURO | **Stack:** Python 3.11, moabb 1.x, scikit-learn, PAX 27B, AIOSS_FORMAT
**Air-gap capable after initial setup.**

## Prerequisites
Python 3.11+, moabb 1.1+, scikit-learn 1.5+. EEG datasets auto-downloaded on first run.

## Environment
8GB RAM. CPU for EEG classification. GPU for PAX interpretation. 10GB disk for MOABB datasets.

## AIOSS Integration
```bash
aioss init --module K_MOABB --output ./k_moabb.aioss
aioss append --chain ./k_moabb.aioss --payload ./output.bin --module K_MOABB
aioss verify --chain ./k_moabb.aioss
```

## Air-Gap Setup
```bash
pip download -r requirements.txt -d ./wheels/
pip install --no-index --find-links ./wheels/ -r requirements.txt
```

## PAX 27B Harness Wiring
```python
from anticloud_pax import PAXHarness
harness = PAXHarness(
    model_path="./pax-27b-q4.gguf",
    module="K_MOABB",
    aioss_chain="./K_MOABB.aioss",
    classification="L5_NARROW_L2_GENERAL"
)
result = harness.process(input_data)
```

## Verification
```bash
aioss verify --chain ./K_MOABB.aioss --verbose
python -m K_MOABB.tests.smoke
```
