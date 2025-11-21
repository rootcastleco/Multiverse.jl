# Contributing

Thank you for your interest in contributing to Multiverse.jl! Contributions can include bug reports, feature requests, documentation improvements, examples, or code changes.

Getting started
1. Fork the repository and create a branch for your changes:
   - git checkout -b feat/your-feature
2. Run tests locally (if available):
   - using Pkg; Pkg.test("Multiverse")
3. Keep changes small and focused. Open a pull request against the main branch with a clear description of the change and motivation.

Code style and guidelines
- Follow standard Julia style conventions.
- Include docstrings for exported functions and types.
- Add unit tests for new features and bug fixes where feasible.
- Run the formatter: import Pkg; Pkg.add("JuliaFormatter"); then format using the project recommended setup.

Documentation
- Add or update wiki pages for new public features.
- Add examples under examples/ or notebooks/ with clear instructions to reproduce results.

Issue workflow
- Open an issue for features or bugs before implementing large changes.
- Tag maintainers using @rootcastleco if you need attention on a design question.

License and CLA
- Contributions are accepted under the repository license. By opening a PR you agree to license your contribution under the same license.

Code of Conduct
- Be respectful and professional in all interactions. Follow the project's Code of Conduct (add link or file if present).

Thank you — contributions help make Multiverse.jl more useful for everyone!