# Quickstart

This quickstart shows a minimal end-to-end example to generate a small multiverse hierarchy and inspect results.

Note: function and type names below are illustrative; consult the API page for exact names if they differ in your installed version.

Minimal example

```julia
using Multiverse, Random

# Set reproducible RNG
rng = MersenneTwister(1234)

# Build a configuration (defaults are chosen to be conservative)
cfg = Multiverse.Config(
    rng = rng,
    n_parents = 1,        # number of parent universes to generate
    child_generations = 3, # number of child generations per parent
    max_children_per_parent = 4
)

# Run the simulation
sim = Multiverse.run(cfg)

# Inspect summary
println("Generated universes: ", length(sim.universes))
println("Sample parent properties: ", sim.universes[1].properties)

# Export results to a DataFrame (if DataFrames is available)
using DataFrames
df = Multiverse.export_dataframe(sim)
first(df, 10)
```

Visual example (simple histogram of a cosmological property)

```julia
using Plots
props = [u.properties[:cosmological_constant] for u in sim.universes]
histogram(props, bins=30, title="Distribution of Λ in generated universes")
```

Tips
- Start with small numbers for generations and children while exploring parameters.
- Use a fixed RNG seed for reproducible experiments.
- Use the export functions to produce CSV/Parquet outputs for downstream analysis.
