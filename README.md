<div align="center">

<img src="demo.gif" width="100%" alt="loopz demo"/>

<br/>

# loopz

### Your Python loops never die again.

<br/>

[![PyPI version](https://badge.fury.io/py/loopz.svg)](https://pypi.org/project/loopz/)
[![Python](https://img.shields.io/pypi/pyversions/loopz)](https://pypi.org/project/loopz)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![PyPI Downloads](https://img.shields.io/pypi/dm/loopz)](https://pypi.org/project/loopz/)
[![GitHub Stars](https://img.shields.io/github/stars/Shiv0087/loopz?style=social)](https://github.com/Shiv0087/loopz)

<br/>

```bash
pip install loopz
```

</div>

---

## The Problem

You've been there.

```
Training ResNet50... epoch 31/50 (62% done)
4 hours in. Loss looking great.

💥 Colab session disconnected.
```

You restart. From **epoch 1**. Again.

**loopz fixes this with one line.**

---

## Quickstart

```python
import loopz

@loopz.track("process_images", save_every=100)
def process(image_path):
    extract_and_save_features(image_path)

process(all_image_paths)
# 💥 crash at 60,000?  run again → resumes at 60,000 ✅
```

That's it. One decorator. One argument. Done.

---

## ML Training — Full State Restore

loopz saves and restores **everything** — model weights, optimizer, scheduler,
GradScaler, accumulators, and even the random seed state — so your training
continues as if the crash never happened.

```python
import loopz
import torch

model     = MyModel()
optimizer = torch.optim.Adam(model.parameters(), lr=1e-3)
scheduler = torch.optim.lr_scheduler.CosineAnnealingLR(optimizer, T_max=50)

running_loss = [0.0]
best_acc     = [0.0]

@loopz.track(
    "training",
    save_every = 1,
    state      = {"model": model, "optimizer": optimizer, "scheduler": scheduler},
    loop_vars  = {"running_loss": running_loss, "best_acc": best_acc},
    notify     = print,
)
def train(epoch):
    loss, acc = train_one_epoch(model, train_loader, optimizer, scheduler)
    running_loss[0] += loss
    best_acc[0]      = max(best_acc[0], acc)
    print(f"Epoch {epoch} | loss={loss:.4f} | acc={acc:.4f}")

train(range(50))
```

```
# 💥 crashes at epoch 31?  just run the script again →

🔁  loopz: Resuming 'training' from 31/50 (62.0% done)
    model ✅  optimizer ✅  scheduler ✅  rng state ✅
    loop_vars: running_loss ✅  best_acc ✅
```

---

## What Gets Saved

| Object | Supported |
|--------|-----------|
| `torch.nn.Module` | ✅ |
| `torch.nn.DataParallel` | ✅ |
| `torch.nn.parallel.DistributedDataParallel` | ✅ |
| `torch.optim.Optimizer` (Adam, SGD, AdamW, …) | ✅ |
| `torch.optim.lr_scheduler.*` | ✅ |
| `torch.cuda.amp.GradScaler` | ✅ |
| `torch.Tensor` | ✅ |
| `numpy.ndarray` | ✅ |
| `sklearn` estimator | ✅ |
| Plain Python object (any picklable) | ✅ |
| Python / NumPy / PyTorch / CUDA random state | ✅ |
| Variables inside the loop (`running_loss`, `best_acc`, …) | ✅ |

---

## How It Works

```
Every save_every-th iteration, loopz atomically writes:

  ~/.loopz/
  ├── loopz_<hash>.json    ← loop position + metadata
  ├── loopz_<hash>.state   ← ML object weights + RNG state
  └── loopz_<hash>.vars    ← your loop accumulators

On crash     → saves final checkpoint, re-raises exception (stack trace visible)
On next run  → detects checkpoint, restores everything, resumes from exact index
On complete  → deletes all saved files automatically (clean slate)
```

---

## API Reference

### `@loopz.track(...)`

```python
@loopz.track(
    job_name   = "my_job",   # unique name — used to find checkpoint on resume
    save_every = 10,         # save checkpoint every N iterations
    state      = {...},      # ML objects to checkpoint (optional)
    loop_vars  = {...},      # accumulators inside the loop (optional)
    notify     = callable,   # called on completion or crash (optional)
)
def process(item):
    ...

process(my_list)
```

---

### `loopz.status()`

See all incomplete jobs at a glance.

```
📋 loopz — 1 saved job(s):

  🔁 training
     Progress : 31/50 (62.0%)
     Saved at : 2026-03-22 14:30:00
     Crashed  : session disconnected at epoch 31
```

---

### `loopz.reset("job_name")`

Delete saved data for a job — starts fresh next run.

### `loopz.reset_all()`

Delete saved data for every job.

---

## Use Cases

| Use Case | How loopz Helps |
|----------|----------------|
| 🤖 ML Training | Saves model, optimizer, scheduler, RNG — resumes deterministically |
| 🖼️ Dataset Processing | Checkpoints every N images — never reprocess what's done |
| 🌐 Web Scraping | Saves progress through URL lists — crash-safe scraping |
| 📥 Bulk Downloads | Resumes from last successful download automatically |
| 🧪 Experiments | Checkpoint any long-running experiment loop |

---

## Limitations

> Honest about what loopz can't do yet.

- **Primitive loop vars** — `int`, `float`, `str` can't be mutated in-place. Wrap in a list: `loss = [0.0]` not `loss = 0.0`
- **Multi-node DDP** — single-machine DDP is supported; multi-node across separate machines is not
- **Custom CUDA C++ ops** — models with non-standard CUDA state may need manual checkpointing alongside loopz
- **Non-picklable objects** — skipped with a warning if they can't be pickled

---

## Contributing

loopz is MIT licensed and open to contributions of all sizes — from typo fixes to new features.

1. Fork the repo
2. Create your branch: `git checkout -b feature/my-feature`
3. Make your change and add tests
4. Open a Pull Request

See [CONTRIBUTING.md](CONTRIBUTING.md) for details.

---

<div align="center">

Built with 💀 after one too many Colab crashes.

**[PyPI](https://pypi.org/project/loopz/) · [Issues](https://github.com/Shiv0087/loopz/issues) · [Discussions](https://github.com/Shiv0087/loopz/discussions)**

<br/>

*If loopz saved you even once — a ⭐ means everything to a solo builder.*

</div>
