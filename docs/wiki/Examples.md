# Examples

This page contains runnable examples and short experiments to get familiar with Multiverse.jl.

Example 1 — Single parent, few generations
```julia
using Multiverse, Random, Plots

cfg = Multiverse.Config(
    rng = MersenneTwister(2025),
    n_parents = 1,
    child_generations = 5,
    max_children_per_parent = 2
)

sim = Multiverse.run(cfg)

# Plot distribution of a property across generations
values_by_gen = [ [u.properties[:Λ] for u in sim.universes if u.generation == g] for g in 0:cfg.child_generations ]
boxplot(values_by_gen, labels=0:cfg.child_generations, xlabel="Generation", ylabel="Λ")
```

Example 2 — Ensemble of parents and export
```julia
using Multiverse, DataFrames, CSV

cfg = Multiverse.Config(n_parents=10, child_generations=3, rng=MersenneTwister(1))
sim = Multiverse.run(cfg)

df = Multiverse.export_dataframe(sim)
CSV.write("multiverse_results.csv", df)
```

Example 3 — Custom callback for lightweight analysis
```julia
function on_generation_finished(sim, gen)
    println("Generation $gen finished. Total universes so far: ", length(sim.universes))
end

cfg = Multiverse.Config(callbacks=[on_generation_finished])
run(cfg)
```

Jupyter/Pluto notebooks
- Examples work well inside Pluto.jl notebooks for interactive tweaking of parameters and immediate plots.
- Consider adding a few example notebooks to the repository under examples/ or notebooks/.

If you want an example translated into your specific research pipeline (file outputs, plotting style, or parameters), open an issue and describe the target output.
