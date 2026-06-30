# Which model should I run?

Find your available RAM in the table below and download that model. That's it.

> "Available RAM" means the RAM on whatever machine is running Docker, not
> your laptop if Docker is running on a separate home server. If you're not
> sure, on Mac check Apple menu > About This Mac. On Windows, check Task
> Manager > Performance > Memory.

| RAM available | Recommended model | Pull command | Best for | Child-safe variant |
|---|---|---|---|---|
| 8GB | Llama 3.2 3B | `ollama pull llama3.2:3b` | Homework help, general chat, simple Q&A | Yes, see `modelfiles/` |
| 16GB | Llama 3.2 3B (same as above, just runs faster with more headroom) | `ollama pull llama3.2:3b` | Same as above, plus smoother multi-user use | Yes, see `modelfiles/` |
| 24GB+ | Qwen 3 8B | `ollama pull qwen3:8b` | Better reasoning, longer conversations, more capable homework help | Yes, see `modelfiles/` (8B template) |
| 48GB (M5 Pro Mac Mini tier) | Run the 8B model for two or three family members at once, no waiting | `ollama pull qwen3:8b` | Whole family using AI simultaneously | Yes, see `modelfiles/` |

**Don't have 8GB free?** You can still run a model, but expect it to feel
slow. Try `ollama pull gemma2:2b` as a lighter fallback.

## What does "Q4" mean?

Q4 means the file is smaller and downloads faster. It is slightly less
precise than the full version, but for everyday family use the difference is
not noticeable. The pull commands above already use Q4 by default, you don't
need to do anything extra.

## When to upgrade

If responses feel slow, repetitive, or start cutting off mid-sentence, your
current model is too large for your hardware. Drop down a tier in the table
above. If responses feel too simple or your kid is asking more advanced
questions than the model handles well, move up a tier instead.

## A note on this table

Model recommendations change fast, this space moves monthly. We re-test
recommendations against real hardware before updating this file. If a model
listed here stops being the best pick, check the repo's issues for an
update, or open one yourself if you've found something better.

Last reviewed: 2026-06-30.
