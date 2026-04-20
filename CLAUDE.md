# CLAUDE.md — VeriStruct

AI-assisted automated verification system for Rust data structures using Verus formal
specifications. Uses LLMs to generate specifications, infer invariants, and repair verification
errors. Research tool (TACAS 2026 paper). Integrated into Project-Metic's formal verification
pipeline as a proof synthesis component.

## What This Repo Is

An LLM-in-the-loop Verus verification system — not a standalone service. VeriStruct automates:
- Generating `requires`/`ensures` clauses and View functions for Rust data structures
- Inferring data structure invariants
- Repairing verification errors via 14+ specialized repair modules
- Running LLM interactions with caching to reduce API costs

Paper: "VeriStruct: AI-assisted Automated Verification of Data-Structure Modules in Verus"
(TACAS 2026). arXiv: https://arxiv.org/abs/2510.25015

## Key Invariants

- **sorry_count == 0 is the correctness bar.** VeriStruct's goal is to produce Verus proofs
  with zero `assume` / `sorry` statements. A run that produces a passing-but-sorry proof is
  not a success by Metic standards.
- **LLM caching is required for production runs.** The caching layer reduces API costs and
  response variance. Never disable caching for batch evaluation runs.
- **Statistics tracking is mandatory.** The `--track-stats` flag must be enabled for any run
  submitted as a benchmark result. Benchmark claims without statistics are not reproducible.
- **Timeout protection is built-in.** The automatic timeout/retry mechanism is the correct path
  for stalled proofs. Do not bypass it with manual process kills.

## Dev Commands

```bash
# Install
pip install -e ".[dev]"

# Run VeriStruct on a Rust file
python veristruct.py --input src/examples/stack.rs --output out/

# Run with LLM caching enabled
python veristruct.py --input src/examples/stack.rs --cache-dir .veristruct-cache --track-stats

# Run tests
pytest tests/ -v
```

## Tech Stack

- Python, Verus (Rust formal verifier), LLM API (Claude / GPT-4)

## Integration with Project-Metic

- Produces Verus proofs consumed by `verus-proof-synthesis` benchmarks
- Proof artifacts may feed into the `VeriStruct` benchmark (849 tasks in VeruSAGE-Bench)
- Lean 4 proof obligations for VeriStruct-verified modules can be discharged via Infera

## What NOT to Do

- Do not count a proof as verified if it contains `assume` or `sorry`
- Do not disable LLM caching for batch evaluation runs
- Do not submit benchmark results without `--track-stats` enabled
