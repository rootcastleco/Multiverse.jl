# Multiverse.jl

> A Julia Framework for Multi-universe Cosmological Inference via the Effort.jl Likelihood Interface

**Author:** Batuhan Ayrıbaş

## Overview

Multiverse.jl is a Julia software package designed to orchestrate and evaluate large ensembles of cosmological model realizations ("universes") against observational constraints through a likelihood-based workflow.

The package centers on a simulation object that couples physical model parameters and observation-derived likelihood terms, with an emphasis on **composability** and a **lightweight user-facing API**.

## Features

- 🔗 **Composable Architecture** - Simulation and Likelihood objects are instantiated independently and connected explicitly
- 🎯 **Minimal Surface Area** - Small number of high-level operations reduces cognitive load
- 🔀 **Separation of Concerns** - Likelihood evaluation delegated to Effort.jl
- 📊 **Planck 2018 Support** - Direct integration with Planck observational data

## Installation

```julia
using Pkg
Pkg.add("Multiverse")
```

## Quick Start

```julia
using Multiverse
using Effort

# Create likelihood object with Planck data
lkl = Likelihood(Planck())

# Configure simulation with model parameters
simulation = Simulation(
    "inftypes", # model tag
    (ns = 0.96, r = 0.02, log10_10As = 3.044)
)

# Initialize simulation with likelihood
Multiverse.init!(simulation, lkl)
```

## Usage

### Likelihood Evaluation

```julia
# Evaluate likelihood for a parameter vector
value = lkl([0.97, 0.1, 3.044])
```

### Best-fit Query

```julia
# Get best-fit parameters and objective value
bf = bestfit(simulation, lkl)
```

## Architecture

```
┌─────────────┐       ┌─────────────────┐       ┌─────────────────┐
│  User Code  │──────▶│  Multiverse.jl  │──────▶│   Effort.jl     │
│   (Julia)   │       │   Simulation    │       │   Likelihood    │
└─────────────┘       │  orchestration  │       │   interface     │
                      └─────────────────┘       └────────┬────────┘
                                                         │
                                                         ▼
                                                ┌─────────────────┐
                                                │ Observational   │
                                                │ Data (Planck)   │
                                                └─────────────────┘
```

## Workflow

1. **Define Model** - Create named-tuple with cosmological parameters
2. **Construct Simulation** - Initialize `Simulation` object with model tag
3. **Construct Likelihood** - Create `Likelihood` via Effort.jl
4. **Initialize** - Bind likelihood to simulation with `Multiverse.init!`
5. **Evaluate** - Run likelihood evaluations in outer loop (grid scan, optimizer, or sampler)
6. **Query Results** - Record values and diagnostics using `bestfit`

## Algorithm

```
Input: simulation configuration S, likelihood object L, parameter set θ

1: Multiverse.init!(S, L)        # bind L to S; perform required setup
2: for each θ in parameter_sets do
3:     ℓ(θ) ← L(θ)               # evaluate likelihood via Effort.jl
4: end for
5: optionally: (ℓ*, θ*) ← bestfit(S, L)

Output: likelihood evaluations {ℓ(θ)} and/or best-fit summary
```

## Complexity

The dominant computational cost is the likelihood evaluation performed by the Effort.jl backend. For `N` parameter sets with each likelihood call costing time `T`:

$$\text{Runtime} = \Theta(NT)$$

## Reproducibility

To ensure computational reproducibility:

- Pin Julia environment using `Project.toml` and `Manifest.toml`
- Record Julia version and platform
- If randomized steps exist, capture seeds and deterministic execution settings
- For observational likelihoods, include precise data release identifiers and checksum-verified downloads

## Related Work

Multiverse.jl interoperates with [Effort.jl](https://github.com/batuhanayribas/Effort.jl) for likelihood computation and is designed as a lightweight orchestration layer for cosmological inference.

Other relevant tools in the ecosystem:
- **CLASS** - Cosmic Linear Anisotropy Solving System
- **CAMB** - Code for Anisotropies in the Microwave Background
- **Turing.jl** - Probabilistic programming in Julia

## References

1. Ayrıbaş, B. (2025). *Effort.jl: A package for cosmological parameter inference using modern programming paradigms*. arXiv:2501.04639.

2. Planck Collaboration. (2020). *Planck 2018 results. VI. Cosmological parameters*. Astronomy & Astrophysics, 641, A6.

3. Bezanson, J., Edelman, A., Karpinski, S., & Shah, V. B. (2017). Julia: A fresh approach to numerical computing. *SIAM Review*, 59(1), 65-98.

4. Lewis, A., Challinor, A., & Lasenby, A. (2000). Efficient computation of CMB anisotropies in closed FRW models. *The Astrophysical Journal*, 538(2), 473-476.

5. Blas, D., Lesgourgues, J., & Tram, T. (2011). The Cosmic Linear Anisotropy Solving System (CLASS). *Journal of Cosmology and Astroparticle Physics*, 2011(07), 034.

## License

[Add your license here]

---

*This README was generated from the academic paper describing Multiverse.jl.*
