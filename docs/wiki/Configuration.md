# Configuration and Parameters

Multiverse.jl is driven by a configuration object that controls stochastic generation, cosmological parameter ranges, and simulation depth. Below is a high-level description of common fields and their intended use.

Common configuration fields
- rng: Random number generator (e.g., MersenneTwister). Controls reproducibility.
- n_parents::Int: Number of independent parent universes to create.
- child_generations::Int: Number of successive generations to spawn from each parent lineage.
- max_children_per_parent::Int: Maximum children a single universe can produce in one generation.
- parameter_distributions::Dict{Symbol,Distribution}: Sampling distributions for cosmological parameters (e.g., H0, Ω_m, Λ).
- eft_cutoff::Float64: Effective Field Theory cutoff scale used by the fluctuation model.
- quantum_fluctuation_strength::Float64: Controls the amplitude of fluctuations that create child universes.
- output_options::Dict: Options controlling how and where results are saved (CSV, JSON, DataFrame, or in-memory).

Creating a configuration

```julia
using Distributions, Random

dists = Dict(
    :H0 => Normal(70.0, 10.0),
    :Ωm => Beta(2, 5),
    :Λ => LogNormal(-120.0, 5.0)
)

cfg = Multiverse.Config(
    rng = MersenneTwister(42),
    n_parents = 5,
    child_generations = 4,
    max_children_per_parent = 3,
    parameter_distributions = dists,
    eft_cutoff = 1e-3,
    quantum_fluctuation_strength = 0.01
)
```

Advanced configuration
- Custom seeding strategies: provide rng per-parent to explore ensemble variance.
- Event hooks: provide callback functions to run custom analysis when a generation finishes.
- Parallel execution: enable threaded or distributed generation to speed up large experiments (see Performance section on main README or Issues).

Validation and safe ranges
- The package will perform lightweight validation on user-supplied distributions and numeric ranges. For scientific rigor, verify generated parameter distributions with independent tools.
