# Installation

This page explains how to install Multiverse.jl and its recommended dependencies.

Prerequisites
- Julia 1.8+ (recommended: latest stable release)
- A working internet connection to download packages

Install via Julia's package manager
1. Open the Julia REPL.
2. Enter the package manager by typing `]`.
3. Add the package from the GitHub repository:

```julia
pkg> add https://github.com/rootcastleco/Multiverse.jl
```

Or, from within a Julia script:

```julia
using Pkg
Pkg.add(url="https://github.com/rootcastleco/Multiverse.jl")
```

Development workflow (editable copy)
If you want to contribute or run the code in development mode:

```julia
using Pkg
Pkg.develop(url="https://github.com/rootcastleco/Multiverse.jl")
```

Recommended companion packages
- Distributions.jl — for sampling cosmological parameters
- Random — for RNG control
- Plots.jl or Makie.jl — for visualization of generated universes
- DataFrames.jl — for tabular output and analysis

Verify installation
Run a quick check in the REPL:

```julia
using Multiverse
Multiverse.version()  # or the equivalent function to print package version
```

If you encounter problems, see Troubleshooting in the FAQ page or open an issue in the repository.
