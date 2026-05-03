<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:03045e,50:0077b6,100:00b4d8&height=220&section=header&text=loopz&fontSize=90&fontColor=ffffff&fontAlignY=42&desc=Your%20Python%20loops%20never%20die%20again.&descAlignY=62&descSize=18" width="100%"/>

<br/>

[![PyPI](https://img.shields.io/badge/PyPI-loopz-00b4d8?style=for-the-badge&logo=pypi&logoColor=white)](https://pypi.org/project/loopz/)
[![Python](https://img.shields.io/badge/Python-3.8+-white?style=for-the-badge&logo=python&logoColor=white&labelColor=03045e)](https://pypi.org/project/loopz/)
[![License](https://img.shields.io/badge/License-MIT-06d6a0?style=for-the-badge)](LICENSE)
[![Stars](https://img.shields.io/github/stars/Shiv0087/loopz?style=for-the-badge&color=00b4d8&logo=github)](https://github.com/Shiv0087/loopz/stargazers)
[![Downloads](https://img.shields.io/pypi/dm/loopz?style=for-the-badge&color=06d6a0&label=installs)](https://pypi.org/project/loopz/)

<br/>

### *"You are not losing code. You are losing time. Your most irreplaceable resource."*

<br/>

</div>

---

## 💀 The Problem Every Developer Has Felt

It is 2 AM.

You started training 6 hours ago. The loss curve is finally going down. The accuracy is climbing. You can see the finish line.

Then your screen goes dark.

```
Epoch 31/50 ━━━━━━━━━━━━━━━━━━━━━━ 62% | loss: 0.38 | acc: 71.4%

💥 Colab session disconnected.
   Your runtime has timed out.
```

You stare at it for a full minute.

Then you restart. From **epoch 1**. Again.

This is not a rare edge case. This is the daily reality of every ML engineer, every data scientist, every student running experiments on a laptop that might die, a Colab that will disconnect, a server that will restart.

> *Every year, millions of GPU-hours are wasted on work that was already done. Not because the models were wrong. Because the infrastructure failed and there was no safety net.*

**loopz is that safety net. One decorator. That is it.**

---

## ⚡ See It In Action

<div align="center">
<img src="demo.gif" width="100%" alt="loopz demo — crash and resume"/>
</div>

---

## 🚀 Install

```bash
pip install loopz
```

---

## 🎯 Quickstart

```python
import loopz

@loopz.track("process_images", save_every=100)
def process(image_path):
    extract_and_save_features(image_path)

process(all_image_paths)
# 💥 crash at 60,000?  just run again → resumes at 60,000 ✅
```

**One decorator. One argument. Done.**

---

## 🧠 ML Training — Full State Resurrection

loopz does not just remember where you were. It remembers **everything.**

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
# 💥 crashes at epoch 31?  run the same script again →

🔁  loopz: Resuming 'training' from 31/50  (62.0% complete)
    ├── model weights    ✅  restored
    ├── optimizer state  ✅  restored
    ├── lr scheduler     ✅  restored
    ├── random seed      ✅  restored  ← deterministic resume
    └── loop variables   ✅  running_loss · best_acc restored

Epoch 31 | loss=0.3821 | acc=71.4%   ← continues exactly here
Epoch 32 | loss=0.3744 | acc=72.1%
...
```

**The crash never happened. Your training never knew.**

---

## 🔐 What Gets Saved

```
On every checkpoint, loopz atomically writes:

  ~/.loopz/
  ├── loopz_<hash>.json    ←  position · timestamps · metadata
  ├── loopz_<hash>.state   ←  model weights · optimizer · rng state
  └── loopz_<hash>.vars    ←  your loop accumulators

atomic write  (temp → rename) = zero corruption risk on crash
```

| Object | Supported |
|--------|:---------:|
| `torch.nn.Module` | ✅ |
| `torch.nn.DataParallel` | ✅ |
| `torch.nn.parallel.DistributedDataParallel` | ✅ |
| `torch.optim.Optimizer` (Adam, SGD, AdamW, …) | ✅ |
| `torch.optim.lr_scheduler.*` | ✅ |
| `torch.cuda.amp.GradScaler` | ✅ |
| `torch.Tensor` | ✅ |
| `numpy.ndarray` | ✅ |
| `sklearn` estimator | ✅ |
| Any picklable Python object | ✅ |
| Python / NumPy / PyTorch / CUDA random state | ✅ |
| Variables inside the loop (`running_loss`, `best_acc`, …) | ✅ |

---

## 🛠️ Full API

### `@loopz.track(...)`

```python
@loopz.track(
    job_name   = "my_job",    # unique name — identifies this job's checkpoint
    save_every = 10,          # checkpoint every N iterations
    state      = {...},       # ML objects to save/restore  (optional)
    loop_vars  = {...},       # accumulators inside the loop (optional)
    notify     = callable,    # called on completion or crash (optional)
)
def process(item):
    ...

process(my_list)
```

### `loopz.status()`

```
📋 loopz — 2 saved job(s):

  🔁 training
     Progress : 31/50  (62.0%)
     Saved at : 2026-04-01 02:14:38
     Crashed  : Colab session disconnected

  🔁 process_images
     Progress : 61,200/100,000  (61.2%)
     Saved at : 2026-04-01 01:58:02
```

### `loopz.reset("job_name")` &nbsp;·&nbsp; `loopz.reset_all()`

Wipe a checkpoint. Start fresh next run.

---

## 💡 Works For Everything

```
🤖 ML Training        →  model + optimizer + scheduler checkpointed every epoch
🖼️  Dataset Processing →  never reprocess what is already done
🌐 Web Scraping       →  crash-safe iteration over URL lists
📥 Bulk Downloads     →  resume from last successful file
🔬 Long Experiments   →  any loop that might not finish in one run
```

---

## ⚠️ Honest Limitations

- **Primitive loop vars** — `int`, `float`, `str` cannot mutate in-place. Wrap them: `loss = [0.0]` not `loss = 0.0`
- **Multi-node DDP** — single-machine DDP works. Multi-node across separate machines does not
- **Custom CUDA C++ ops** — non-standard CUDA state may need manual checkpointing alongside loopz
- **Non-picklable objects** — skipped with a warning

---

## 🗺️ Roadmap

```
v0.1  ████████████  core decorator + resume + ML state  ✅ shipped
v0.2  ████████░░░░  tqdm integration + async support    ✅ shipped
v0.3  ██████░░░░░░  notify hooks + Telegram/webhook     🔄 in progress
v0.4  ████░░░░░░░░  web dashboard for job status        📅 planned
v0.5  ██░░░░░░░░░░  multi-node DDP support              📅 planned
```

---

## 🤝 Contributing

loopz is MIT licensed and built for the community.

If you have ever lost training progress — you already understand this project deeply enough to contribute.

1. Fork the repo
2. `git checkout -b feature/your-idea`
3. Make your change, add a test
4. Open a Pull Request — all sizes welcome

---

## 👨‍💻 Built By

Built by a solo developer **from India** — after losing hours of Colab training one too many times.

This is not just a project. It is a frustration turned into a tool. Every ML student who has ever stared at a disconnected Colab session and felt their stomach drop — **this is for you.**

---

<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:00b4d8,50:0077b6,100:03045e&height=120&section=footer" width="100%"/>

**[⭐ Star this repo](../../stargazers) &nbsp;·&nbsp; [📦 PyPI](https://pypi.org/project/loopz/) &nbsp;·&nbsp; [🐛 Issues](../../issues)**

*A ⭐ from you means everything to a solo builder.*

</div>
