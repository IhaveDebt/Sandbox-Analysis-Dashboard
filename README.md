
---

# 4 — Sandbox Analysis Dashboard (sandbox_dashboard.py) — defensive-only simulation

**File:** `src/sandbox_dashboard.py`
```python
#!/usr/bin/env python3
"""
Sandbox Analysis Dashboard (simulation) - sandbox_dashboard.py

- Ingests synthetic sandbox JSON outputs (harmless simulated behavior)
- Indexes behavior types and clusters similar samples
- Provides simple CLI queries to explore samples
"""

import json
from collections import defaultdict, Counter
import math
import os
from typing import List

def load_samples(folder):
    samples = []
    for fname in os.listdir(folder):
        if fname.endswith(".json"):
            with open(os.path.join(folder, fname)) as f:
                samples.append(json.load(f))
    return samples

def fingerprint(sample):
    # simple fingerprint: sorted unique behaviors
    return tuple(sorted(set(sample.get("behaviors", []))))

def cluster(samples):
    clusters = defaultdict(list)
    for s in samples:
        clusters[fingerprint(s)].append(s)
    return clusters

def summary(samples: List[dict]):
    cnt = Counter()
    for s in samples:
        for b in s.get("behaviors", []):
            cnt[b] += 1
    return cnt.most_common(20)

def demo(folder="samples"):
    samples = load_samples(folder)
    print(f"Loaded {len(samples)} samples")
    print("Top behaviors:", summary(samples)[:10])
    cl = cluster(samples)
    print("Cluster counts:", {k: len(v) for k, v in cl.items()})

if __name__ == "__main__":
    demo()
