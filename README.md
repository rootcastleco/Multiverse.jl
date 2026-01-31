\documentclass[11pt]{article}
\usepackage[margin=1in]{geometry}
\usepackage{amsmath}
\usepackage{amssymb}
\usepackage{graphicx}
\usepackage{booktabs}
\usepackage{listings}
\usepackage{xcolor}
\usepackage{tikz}
\usetikzlibrary{positioning,arrows.meta,shapes.geometric}
\usepackage{tikz-cd}
\usepackage[hidelinks]{hyperref}

\lstdefinelanguage{Julia}{%
  morekeywords={abstract,break,case,catch,const,continue,do,else,elseif,end,export,false,finally,for,function,global,if,import,in,let,local,macro,module,mutable,quote,return,struct,true,try,using,where,while},
  sensitive=true,
  morecomment=[l]\#,
  morestring=[b]",
}
\lstset{%
  language=Julia,
  basicstyle=\ttfamily\small,
  keywordstyle=\color{blue!70!black},
  commentstyle=\color{black!55},
  stringstyle=\color{red!60!black},
  breaklines=true,
  columns=fullflexible,
  frame=single,
  framerule=0.3pt,
  rulecolor=\color{black!20},
}

\setlength{\parindent}{0pt}
\setlength{\parskip}{1\baselineskip}

\title{Multiverse.jl: A Julia Framework for Multi-universe Cosmological Inference via the Effort.jl Likelihood Interface}
\author{Batuhan Ayr\i ba\c{s}}
\date{}

\begin{document}
\maketitle

\begin{abstract}
Multiverse.jl is a Julia software package designed to orchestrate and evaluate large ensembles of cosmological model realizations (``universes'') against observational constraints through a likelihood-based workflow.
The package centers on a simulation object that couples physical model parameters and observation-derived likelihood terms, with an emphasis on composability and a lightweight user-facing API.
This article describes the motivation for Multiverse.jl, summarizes its exposed interfaces and workflow as documented in the repository, and proposes a reproducible evaluation methodology.
The present repository does not include published benchmark scripts or a curated performance report; therefore, quantitative results are restricted to example outputs provided in the documentation, and a benchmarking plan is outlined.
\end{abstract}

\section{Introduction}
Modern cosmological inference increasingly relies on computational pipelines that connect a generative physical model to heterogeneous observational constraints.
While comprehensive ecosystems exist across several languages, a recurring challenge is to retain a high-level, modular user experience while supporting scalable evaluation across many candidate parameter sets.

In a typical likelihood-based pipeline, scientific productivity is determined not only by the physical fidelity of the forward model, but also by the ergonomics of repeatedly invoking the likelihood under varying parameterizations.
This becomes particularly salient in exploratory phases, where researchers may iterate over alternative model families, modify parameter priors, or introduce new likelihood terms, and where small interface mismatches can lead to substantial engineering overhead.

Julia is well-suited to this setting because it supports high-level abstractions without abandoning performance, enabling domain researchers to prototype analysis logic in the same language used for production-quality numerics.
However, the benefit of a Julia-native workflow depends on stable and composable APIs that connect models, likelihood evaluations, and higher-level inference loops.

Multiverse.jl is motivated by this gap: it aims to provide a clear Julia-native workflow for evaluating many ``universe'' realizations against observational likelihoods.
In the repository documentation, the package is presented as a ``multiverse simulator'' for cosmology, where users define a simulation, connect it to a likelihood provider, and query or scan the resulting objective.
The design, as evidenced by the README usage example, is intentionally minimal: users work primarily with a \texttt{Simulation} object, a \texttt{Likelihood} object (from Effort.jl), and a small set of functions for initialization and evaluation.

The problem addressed in this article is therefore not the derivation of new cosmological theory, but the software engineering problem of building a composable interface for repeated likelihood evaluation in Julia.

\section{Related Work}
Multiverse.jl is positioned to interoperate with Effort.jl, which provides a likelihood interface for cosmological parameter inference.
In this sense, Multiverse.jl resembles a lightweight orchestration layer sitting above a likelihood backend: it focuses on expressing an ensemble evaluation problem and delegating likelihood computation.

More broadly, cosmological inference pipelines often connect Boltzmann solvers and likelihood modules (e.g., CLASS, CAMB, and associated likelihood codes) through sampling frameworks.
In Julia, probabilistic programming systems (e.g., general-purpose MCMC frameworks) provide machinery for posterior inference, but do not necessarily prescribe a domain-specific representation of ``universe'' realizations or their coupling to cosmology likelihoods.
Consequently, Multiverse.jl occupies a niche defined by (i) a domain vocabulary tailored to cosmology and (ii) a thin, composable API that integrates directly with an external likelihood provider.

Because the repository documentation does not include explicit comparisons or benchmarks against alternative pipelines, this article limits claims to interface-level distinctions and proposes evaluation criteria suitable for future comparisons.

A useful point of reference is the broader class of inference systems that separate (i) a parameter-to-prediction mapping (the ``forward model'') from (ii) a likelihood evaluator and (iii) a sampling or optimization driver.
In this decomposition, Multiverse.jl appears to focus on the boundary between (i) and (ii): it standardizes how a simulation configuration is prepared and connected to an external likelihood implementation.

In contrast, probabilistic programming frameworks (e.g., systems that embed models as generative programs) tend to emphasize posterior inference and automatic differentiation, but typically place fewer constraints on a domain-specific workflow or on explicit ``simulation'' objects.
Multiverse.jl is compatible with these approaches to the extent that it yields a stable objective function that can be called from external drivers.

\section{System Design and Architecture}
\subsection{High-level components}
The public workflow described in the repository consists of:
\begin{itemize}
  \item a \texttt{Simulation} object constructed from a model configuration (e.g., cosmological and inflationary parameters),
  \item a \texttt{Likelihood} object constructed via Effort.jl (e.g., Planck 2018 data),
  \item an initialization step that binds the likelihood to the simulation, and
  \item a query interface that evaluates the likelihood for a given parameter set and/or provides best-fit information.
\end{itemize}

The following diagram summarizes this architecture at the level supported by repository documentation.

\begin{figure}[h]
  \centering
  \begin{tikzpicture}[node distance=1.5cm, font=\small]
    \node[draw, rounded corners, align=center] (user) {User code \\ (Julia)};
    \node[draw, rounded corners, align=center, right=2.2cm of user] (mv) {Multiverse.jl \\ \texttt{Simulation} \\ orchestration layer};
    \node[draw, rounded corners, align=center, right=2.2cm of mv] (effort) {Effort.jl \\ \texttt{Likelihood} \\ interface};
    \node[draw, rounded corners, align=center, below=1.3cm of effort] (data) {Observational data \\ (e.g., Planck 2018)};

    \draw[->] (user) -- node[above]{construct / configure} (mv);
    \draw[->] (user) -- node[above]{select likelihood} (effort);
    \draw[->] (mv) -- node[above]{\texttt{init!}} (effort);
    \draw[->] (effort) -- node[right]{loads / uses} (data);
    \draw[->] (user) to[bend right=15] node[below]{\texttt{lkl(...)} / queries} (mv);
  \end{tikzpicture}
  \caption{Documented high-level architecture of Multiverse.jl. The repository README demonstrates that likelihood computation is delegated to Effort.jl, while Multiverse.jl maintains the simulation-level orchestration and user-facing queries.}
\end{figure}

\subsection{Workflow}
Based on the documented example, the intended workflow is:
(1) instantiate a \texttt{Simulation} with a model name and a named-tuple of parameters,
(2) instantiate a likelihood object via Effort.jl,
(3) initialize the simulation against the likelihood via \texttt{Multiverse.init!}, and
(4) evaluate the likelihood and query best-fit diagnostics.

In practice, this workflow corresponds to an iterative outer loop (grid scan, optimizer, or sampler) that repeatedly proposes a parameter vector and requests the resulting likelihood value.
Multiverse.jl is therefore best understood as an adapter that stabilizes the interface between the user-defined simulation configuration and the likelihood engine, rather than as a monolithic inference framework.

\begin{figure}[h]
  \centering
  \begin{tikzpicture}[font=\small, node distance=1.2cm]
    \node[draw, rounded corners, align=center] (cfg) {Define model \\ (named-tuple parameters)};
    \node[draw, rounded corners, align=center, below=of cfg] (sim) {Construct \texttt{Simulation}};
    \node[draw, rounded corners, align=center, below=of sim] (lik) {Construct \texttt{Likelihood} \\ (Effort.jl)};
    \node[draw, rounded corners, align=center, below=of lik] (init) {\texttt{Multiverse.init!}};
    \node[draw, rounded corners, align=center, below=of init] (loop) {Outer loop: propose parameters \\ and call \texttt{lkl(\dots)}};
    \node[draw, rounded corners, align=center, below=of loop] (out) {Record values / diagnostics \\ (e.g., \texttt{bestfit})};

    \draw[-{Latex}] (cfg) -- (sim);
    \draw[-{Latex}] (sim) -- (lik);
    \draw[-{Latex}] (lik) -- (init);
    \draw[-{Latex}] (init) -- (loop);
    \draw[-{Latex}] (loop) -- (out);
  \end{tikzpicture}
  \caption{Documented end-to-end workflow pattern in Multiverse.jl, emphasizing the separation between simulation configuration, likelihood construction, initialization, and repeated evaluation.}
\end{figure}

\subsection{Design principles (inferred)}
Although the internal source code is not analyzed in this manuscript, the exposed usage pattern implies several design principles.
First, \emph{composability}: a \texttt{Simulation} and a \texttt{Likelihood} are instantiated independently and connected explicitly via \texttt{init!}, suggesting a preference for explicit dependency injection over hidden global state.
Second, \emph{minimal surface area}: the documented API relies on a small number of high-level operations (construction, initialization, evaluation, and best-fit query), which reduces cognitive load for downstream integration.
Third, \emph{separation of concerns}: likelihood evaluation is delegated to Effort.jl, allowing Multiverse.jl to focus on representing and organizing ensembles of ``universes'' rather than re-implementing likelihood machinery.

\subsection{Reproducibility considerations}
To ensure computational reproducibility, a Multiverse.jl-based analysis should pin the Julia environment using \texttt{Project.toml} and (when available) \texttt{Manifest.toml}, and should record the Julia version and platform.
If the likelihood backend performs any randomized internal steps (not established by the repository excerpt), seeds and deterministic execution settings should be captured.
Finally, because observational likelihoods may require external data files, the precise data release identifiers and checksum-verified downloads should be included in any published artifact.

\section{Key Algorithms and Implementation Details}
The repository excerpt available through the README suggests that Multiverse.jl exposes a small, direct API.
Since the full source code is not visible in the documentation excerpt, the discussion below is restricted to the public-facing behavior shown.

\subsection{Representative usage}
The following Julia snippet reproduces the documented setup pattern, including a concrete likelihood choice (Planck 2018).

\begin{lstlisting}
using Multiverse
using Effort

lkl = Likelihood(Planck())

simulation = Simulation(
    "inftypes", # model tag
    (ns = 0.96, r = 0.02, log10_10As = 3.044)
)

Multiverse.init!(simulation, lkl)
\end{lstlisting}

\subsection{Evaluation interface}
The README indicates that the likelihood can be evaluated by calling \texttt{lkl} on a parameter container (shown as a vector).
It also demonstrates a best-fit query returning both a scalar objective and a parameter set.

\begin{lstlisting}
# Example evaluation call (as documented)
value = lkl([0.97, 0.1, 3.044])

# Best-fit query (as documented)
bf = bestfit(simulation, lkl)
\end{lstlisting}

\subsection{Algorithmic workflow (pseudocode)}
The following pseudocode captures the minimal workflow implied by the API, without asserting internal details.

\begin{verbatim}
Algorithm 1: Initialization and evaluation loop
Input: simulation configuration S, likelihood object L, parameter set theta

1: Multiverse.init!(S, L)        # bind L to S; perform any required setup
2: for each theta in parameter_sets do
3:     ell(theta) <- L(theta)    # evaluate likelihood via Effort.jl interface
4: end for
5: optionally: (ell*, theta*) <- bestfit(S, L)
Output: likelihood evaluations {ell(theta)} and/or best-fit summary
\end{verbatim}

\subsection{Complexity considerations}
Given the available documentation, the dominant computational cost is expected to be the likelihood evaluation performed by the Effort.jl backend for each parameter set.
If \texttt{N} parameter sets are evaluated and each likelihood call costs \texttt{T} time, then end-to-end runtime is \(\Theta(NT)\) absent memoization or parallelization.
Whether Multiverse.jl introduces caching, vectorized evaluation, or distributed execution cannot be concluded from the repository excerpt and should be verified from the package source.

\section{Use Cases and Experimental Results}
\subsection{Documented example: Planck likelihood evaluation}
The README provides an example of evaluating the Planck likelihood for a specific parameter vector and reports a numeric output, indicating that the code path successfully computes and returns a scalar likelihood quantity.
It also reports a best-fit query returning a parameter triple.
These examples serve as a basic functional demonstration rather than a controlled performance experiment.

\subsection{Proposed Evaluation}
The repository excerpt does not include benchmark scripts, profiling reports, or comparisons.
Accordingly, the following evaluation plan is proposed to characterize performance and usability in a reproducible manner:

\textbf{Baselines.} A minimal baseline is direct Effort.jl likelihood evaluation without Multiverse.jl orchestration (where possible), to quantify orchestration overhead.
If alternative Julia-based cosmological likelihood wrappers exist, they can serve as additional baselines.

\textbf{Workloads.} (i) single-point evaluation, (ii) dense grid scans over \((n_s, r, \log_{10}(10^{10}A_s))\), and (iii) sampler-driven sequences (e.g., MCMC or nested sampling) using Multiverse.jl solely as the objective provider.

\textbf{Metrics.} Wall-clock time per likelihood call, throughput (evaluations/s), memory allocation per call, and end-to-end runtime for fixed scan sizes.
Correctness metrics include agreement with reference likelihood outputs for selected test points (when reference values are available).

\textbf{Methodology.} Pin Julia version and dependencies using \texttt{Project.toml/Manifest.toml} when present; perform warm-up runs; repeat each measurement (e.g., 30--100 iterations) and report mean and confidence intervals.

\textbf{Threats to validity.} Cosmology likelihoods may include expensive initialization and nontrivial I/O; results are sensitive to caching, threading, and hardware.
Therefore, experiments must separate one-time initialization costs from steady-state evaluation and fully specify the environment.

\section{Discussion}
\subsection{Implications and strengths}
The primary contribution of Multiverse.jl, as supported by the repository documentation, is a compact, domain-aligned API for expressing cosmological ``multiverse'' evaluation problems within Julia.
By structuring the workflow around a \texttt{Simulation} object and delegating observational likelihood computation to Effort.jl, Multiverse.jl encourages separation of concerns: physical model configuration remains distinct from the likelihood interface.
This separation can improve composability when integrating inference methods, parameter scans, or higher-level workflows.

\subsection{Limitations}
This article is constrained by the repository excerpt available through the README.
Without access to full internal modules, tests, and CI configuration, it is not possible to audit error handling, extension points, or performance-related implementation details.
Moreover, no benchmark suite is provided in the excerpt; therefore, claims about scalability and state-of-the-art performance are necessarily limited.

\subsection{Future directions}
A natural next step is to add a first-class, reproducible benchmarking harness (e.g., scripted parameter scans with fixed seeds) and to document scaling behavior with respect to likelihood complexity and ensemble size.
If parallel evaluation is a target (as suggested by the ``multiverse'' framing), an explicit parallel execution model and determinism guarantees should be documented and tested.

\section{Conclusion}
Multiverse.jl provides a Julia-centric workflow for evaluating ensembles of cosmological model realizations against observational likelihoods via integration with Effort.jl.
The repository documentation emphasizes a minimal, composable interface centered on a simulation object, likelihood initialization, and direct evaluation and best-fit queries.
While quantitative performance claims cannot be substantiated from the available repository excerpt, the package establishes a clear architectural separation between simulation orchestration and likelihood computation, and it provides a concrete starting point for reproducible evaluation and future extensions.

\section*{References}
\begin{thebibliography}{12}
\bibitem{effort2025}
Ayr\i ba\c{s}, B. (2025).
\textit{Effort.jl: A package for cosmological parameter inference using modern programming paradigms}.
arXiv:2501.04639.

\bibitem{planck2018}
Planck Collaboration. (2020).
\textit{Planck 2018 results. VI. Cosmological parameters}.
Astronomy \& Astrophysics, 641, A6.

\bibitem{julia2017}
Bezanson, J., Edelman, A., Karpinski, S., \& Shah, V. B. (2017).
Julia: A fresh approach to numerical computing.
\textit{SIAM Review}, 59(1), 65--98.

\bibitem{camb2000}
Lewis, A., Challinor, A., \& Lasenby, A. (2000).
Efficient computation of CMB anisotropies in closed FRW models.
\textit{The Astrophysical Journal}, 538(2), 473--476.

\bibitem{class2011}
Blas, D., Lesgourgues, J., \& Tram, T. (2011).
The Cosmic Linear Anisotropy Solving System (CLASS).
\textit{Journal of Cosmology and Astroparticle Physics}, 2011(07), 034.

\bibitem{stan2017}
Carpenter, B., Gelman, A., Hoffman, M. D., Lee, D., Goodrich, B., Betancourt, M., Brubaker, M., Guo, J., Li, P., \& Riddell, A. (2017).
Stan: A probabilistic programming language.
\textit{Journal of Statistical Software}, 76(1).

\bibitem{turing2021}
Ge, H., Xu, K., \& Ghahramani, Z. (2018).
Turing: a language for flexible probabilistic inference.
\textit{Proceedings of the 21st International Conference on Artificial Intelligence and Statistics (AISTATS)}.

\bibitem{dynhmc2018}
Hoffman, M. D., \& Gelman, A. (2014).
The No-U-Turn sampler: adaptively setting path lengths in Hamiltonian Monte Carlo.
\textit{Journal of Machine Learning Research}, 15(1), 1593--1623.
\end{thebibliography}

\end{document}
