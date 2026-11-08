# Developer Cookbook — K_MOABB
**Stack:** Python 3.11, moabb 1.x, scikit-learn, PAX 27B, AIOSS_FORMAT
**Domain:** MOABB: BCI benchmark for EEG classification models in Anticloud

## Run MOABB benchmark
```python
from k_moabb import MOABBBenchmark

bench = MOABBBenchmark(
    pax_model="./pax-27b-q4.gguf",
    aioss_chain="./moabb.aioss"
)

result = bench.evaluate(
    model=my_eeg_classifier,  # scikit-learn compatible
    paradigms=["MotorImagery", "P300", "SSVEP"],
    n_datasets_per_paradigm=3
)
print(f"Mean accuracy: {result.mean_accuracy:.3f}")
print(f"Across datasets: {result.per_dataset_accuracy}")
print(f"Deploy recommended: {result.deploy_recommended}")
print(f"Chain: {result.chain_hash}")
```

## AIOSS Chain Append
```python
import hashlib, time

def aioss_append(chain_path, payload: bytes, module_id: str):
    entry_hash = hashlib.sha3_256(payload).digest()
    ts = int(time.time_ns()).to_bytes(8, 'big')
    with open(chain_path, 'rb') as f:
        f.seek(-32, 2); prev_hash = f.read(32)
    new_hash = hashlib.sha3_256(prev_hash + entry_hash + ts).digest()
    with open(chain_path, 'ab') as f:
        f.write(ts + entry_hash + new_hash)
    return new_hash.hex()
```
