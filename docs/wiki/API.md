# API Reference (high level)

This page summarizes the primary types and functions you will use. For full details, consult docstrings in the package source.

Primary types
- Multiverse.Config — configuration container for a run (see Configuration page).
- Multiverse.Simulation — holds the results of a run, including all generated universes and metadata.
- Multiverse.Universe — representation of a single universe (properties, parent/child links, metadata).
- Multiverse.Parameters — typed container for cosmological parameters sampled for a universe.

Primary functions
- run(cfg::Multiverse.Config) -> Multiverse.Simulation
  Execute a multiverse generation using the provided configuration.

- sample_parameters(cfg::Multiverse.Config, rng) -> Multiverse.Parameters
  Draw a parameter set according to configuration distributions.

- spawn_children(parent::Multiverse.Universe, cfg::Multiverse.Config, rng) -> Vector{Universe}
  Generate child universes from a given parent using the fluctuation model.

- export_dataframe(sim::Multiverse.Simulation) -> DataFrame
  Flatten simulation results into a DataFrame for analysis.

- save(sim::Multiverse.Simulation; path, format=:json)
  Save a simulation to disk in the requested format.

Utility and analysis helpers
- summary(sim::Multiverse.Simulation)
  Print a human-readable summary of the run.

- lineage(sim::Multiverse.Simulation, u::Multiverse.Universe)
  Return the ancestry chain of a universe.

- statistics(sim::Multiverse.Simulation; by=:generation)
  Produce aggregated statistics grouped by generation, parent, or other keys.

Docstrings and autocomplete
- Use the Julia REPL help mode:

```julia
?Multiverse.run
```

This will print the function docstring and available signatures.

Notes
- The API above is intentionally high-level. Names may evolve. Always check your installed package version's docstrings for accurate signatures.
