<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:0d1b2a,100:00c9c8&height=180&section=header&text=Contributing%20to%20loopz&fontSize=42&fontColor=ffffff&fontAlignY=42&desc=Every%20great%20library%20is%20built%20by%20a%20community.%20Start%20here.&descAlignY=62&descSize=15" width="100%"/>

</div>

<br/>

First of all — thank you. loopz is a solo-built, open source tool that exists because developers were tired of losing progress. If you are here, you probably felt that pain too. That already makes you the right person to contribute.

Every contribution matters. A typo fix. A better error message. A new feature. All of it moves the project forward.

---

## 📋 Table of Contents

- [Code of Conduct](#-code-of-conduct)
- [Ways to Contribute](#-ways-to-contribute)
- [Reporting Bugs](#-reporting-bugs)
- [Requesting Features](#-requesting-features)
- [Development Setup](#-development-setup)
- [Making a Pull Request](#-making-a-pull-request)
- [Commit Messages](#-commit-messages)
- [Good First Issues](#-good-first-issues)
- [License](#-license)

---

## 🤝 Code of Conduct

Be kind. Be constructive. Be patient.

loopz is built for developers of all backgrounds and experience levels. Harassment, gatekeeping, or dismissive behavior will not be tolerated. If you see it, report it by opening an issue or emailing the maintainer directly.

---

## 💡 Ways to Contribute

You do not need to write code to contribute. Here is what actually helps:

| Type | Examples |
|------|---------|
| 🐛 Bug reports | Something crashes, behaves unexpectedly, or gives a wrong result |
| 📖 Docs | Improve the README, fix typos, add examples, clarify confusing sections |
| ✨ Features | New checkpoint targets, better CLI output, new notify integrations |
| 🧪 Tests | Add test cases, improve coverage, catch edge cases |
| 🎨 DX improvements | Better error messages, warnings, progress output |
| ⭐ Spreading the word | Star the repo, share it, write about it |

---

## 🐛 Reporting Bugs

Found something broken? Please open an [Issue](../../issues) and include:

```
**What happened:**
loopz crashed when I passed a generator instead of a list.

**What I expected:**
It should either work or give a clear error message.

**Steps to reproduce:**
1. import loopz
2. @loopz.track("job", save_every=10)
3. pass a generator object
4. see the error

**Environment:**
- Python version: 3.10
- loopz version: pip show loopz
- OS: Ubuntu 22.04 / Colab / Windows 11
- PyTorch version (if relevant): 2.1.0
```

The more detail you give, the faster it gets fixed.

---

## ✨ Requesting Features

Have an idea? Open an [Issue](../../issues) with the label `feature request` and include:

- **The problem** — what are you trying to do that loopz currently can't?
- **Your proposed solution** — how would you want it to work?
- **Alternatives you considered** — why those don't work for you

> 💡 Tip: Check [existing issues](../../issues) first — your idea might already be tracked.

---

## 🛠️ Development Setup

```bash
# 1. Fork the repo on GitHub, then clone your fork
git clone https://github.com/<your-username>/loopz.git
cd loopz

# 2. Create a virtual environment
python -m venv venv
source venv/bin/activate      # Windows: venv\Scripts\activate

# 3. Install loopz in editable mode
pip install -e .

# 4. Create your branch
git checkout -b feature/your-idea
```

---

## 🔁 Making a Pull Request

```bash
git add .
git commit -m "feat: your change here"
git push origin feature/your-idea
```

Then open a Pull Request to `Shiv0087/loopz` and include:

- **What changed** and why
- **Issue link** if applicable — e.g. `Closes #12`
- **How to test it** — what should the reviewer run to verify it works

---

## 📝 Commit Messages

```
fix: handle non-picklable objects gracefully
feat: add support for telegram notify hook
docs: add example for sklearn checkpointing
test: add edge case for empty list input
refactor: simplify checkpoint path hashing
```

Types: `fix` `feat` `docs` `test` `refactor` `chore`

---

## 🌱 Good First Issues

Never contributed to open source before? Start here:

- Look for issues tagged [`good first issue`](../../issues?q=is%3Aissue+label%3A%22good+first+issue%22)
- Improve an error message to be more helpful
- Add a missing example to the README
- Write a test for an edge case

You do not need to be an expert. You just need to care.

---

## 📄 License

By contributing, you agree that your contributions will be licensed under the **MIT License** — the same license as loopz.

---

<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:00c9c8,100:0d1b2a&height=120&section=footer" width="100%"/>

*Built with 💀 after one too many Colab crashes.*

**[⭐ Star loopz](../../stargazers) &nbsp;·&nbsp; [🐛 Open an Issue](../../issues) &nbsp;·&nbsp; [📦 PyPI](https://pypi.org/project/loopz/)**

</div>
