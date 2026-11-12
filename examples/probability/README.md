# Genesis Probability Lab

The lab flips a coin or rolls a die inside a physics simulation and shows how the odds of each outcome build up.

The simulation engine is [Genesis World](https://github.com/Genesis-Embodied-AI/genesis-world) (Apache-2.0). This folder holds an example application built on top of it.

## Demo

https://github.com/user-attachments/assets/2c857a52-bca0-4b52-ae38-52206615a5d1

The video runs about 77 seconds and has captions. A full-quality copy (1080p, 31 MB) is kept in this folder as [Physical_AI_Probability.mp4](Physical_AI_Probability.mp4?raw=true), which may download first depending on the browser.

The video shows a coin flip and a die roll, the forecast curves while a toss is still in the air, and a batch of 1,000 die rolls filling the frequency table.

## How it works

- The left half of the window shows a coin or die dropped onto a floor. Genesis integrates its motion and its contacts with the rigid-body solver.
- Nothing picks the result at random. The face on top is read once the body has come to rest. The only randomness is in the launch state (orientation, spin, small velocity changes), and each trial records its own seed.
- The right half shows two things. The forecast starts from the state observed during a toss at 0, 0.30 and 0.65 seconds, runs 64 slightly perturbed futures to the end, and reports how many ended on each face. The observed outcomes table counts finished trials and gives a 95% interval per face.
- A coin that stays on its edge, or runs past the 6 second limit, is counted as unresolved. It is never given a face.

## Running the lab

The lab was developed on Windows 11 with Python 3.12, an NVIDIA GPU (CUDA build of PyTorch) and Genesis 1.4.6.

The setup is a `.venv` in the repository root with PyTorch installed and this repository installed through `pip install -e ".[dev]"`. The dev extras include the interface library (`imgui-bundle`) and SciPy.

The lab starts by double-clicking `Run-Probability-Lab.cmd`, or by running:

```powershell
.venv\Scripts\python.exe -m examples.probability.app -g
```

Without `-g`, the lab runs on the CPU, which is slower. The first launch of an object or batch size spends about a minute compiling kernels.

In the window, Coin or Die is chosen under Experiment settings. Flip coin or Roll die starts a single toss, and a batch runs 128 trials by default. Export JSON + CSV writes to `out/probability/`, and closing the window also saves the finished trials.

A fixed-size experiment runs without a window through a command like this:

```powershell
.venv\Scripts\python.exe -m examples.probability.app -g --kind die --batch 256
```

## What the numbers mean

- 50% per side for the coin and 1/6 per face for the die are reference values from symmetry. Genesis does not impose them, so a gap from them is something to investigate and not something the engine forces.
- The coin is an idealized cylinder (45 mm radius, 8 mm thick, 20 g). It is not calibrated to any real coin.
- A small batch can sit away from the reference by chance. In one 256-toss coin batch with seed 2026, 98 tosses ended unresolved, so that batch says little about 50/50 behavior of the coin overall (see `DETAILS.md`).
- The simulation has not been compared with real tossed coins or dice yet. The comparison tools listed below ship with synthetic placeholder charts, and the charts are labeled as such.

## Files

| Path | Purpose |
| --- | --- |
| `app.py` | The window and the command line |
| `worker.py` | A background worker that runs the physics and sends frames and forecasts to the window |
| `world.py` | Builds the Genesis scene: floor, coin or die, contact and friction settings |
| `compare_real.py` | Compares tracked real tosses with Genesis replays of their release states (see `COMPARISON.md`) |
| `published_reference.py` | Compares a coin batch with a published real-world experiment (see `PUBLISHED_COMPARISON.md`) |
| `DETAILS.md` | The full method, conventions, statistics and validation notes |

The tests are in `tests/core/test_probability*.py`.
