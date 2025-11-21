# FAQ & Troubleshooting

Q: I get different results on different machines — why?
A: Ensure you are using a fixed RNG seed (e.g., MersenneTwister with the same integer seed) and the same package versions. Also confirm Julia versions — small numeric differences can arise across Julia releases.

Q: How can I run large experiments efficiently?
A: Use smaller test runs to tune parameters. Enable parallel or distributed execution (if supported). Save intermediate results and avoid holding very large in-memory structures — export to disk in batches.

Q: Are the generated physical parameter distributions physically realistic?
A: Multiverse.jl provides stochastic generators consistent with EFT-inspired priors. The choices of priors and EFT cutoff determine realism. The package is a tool for exploration; users should validate outcomes against domain-specific expectations.

Q: How do I visualize lineage trees?
A: Export the lineage and use graph visualization libraries (GraphPlot.jl, Graphs.jl + GraphRecipes) or external tools (Gephi). The export function produces parent-child edges suitable for such tools.

Q: Where do I report bugs or request features?
A: Open an issue on the GitHub repository with reproduction steps and, if possible, a minimal failing example.

Q: The package API differs from examples in the wiki — what should I do?
A: The wiki contains high-level examples. Always check installed package docstrings and the repository source for authoritative names and signatures. If the docs are out of date, please open a documentation PR.
