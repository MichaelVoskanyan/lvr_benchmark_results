# LVR Benchmark Results

This repository contains the benchmark artifacts and reduction results used in the evaluation of **LVulnReducer (LVR)**, an HDD-first, LLM-assisted reducer for vulnerability-preserving source reduction.

The results are organized by project and CVE. For each benchmark instance, this repository includes the original vulnerable source file, the final reductions produced by **C-Reduce**, **LVR**, and **HDD**, the benchmark-specific interestingness scripts used during reduction, and the Clang AST JSON files used for statement-level comparison.

## Repository Layout

```text
lvr_benchmark_results/
    libtiff/
        CVE-2016-9532/
        CVE-2016-10092/
        CVE-2016-10094/
        CVE-2016-10272/
        CVE-2017-5225/
        CVE-2017-7595/
        CVE-2017-7599/
        CVE-2017-7600/
        CVE-2017-7601/
    libjpeg/
        CVE-2012-2806/
        CVE-2017-15232/
        CVE-2018-14498/
    jasper/
        CVE-2016-8691/
        CVE-2016-9557/
    libarchive/
        CVE-2016-5844/
```

Each CVE directory contains files of the following form:
- `targetfile.c.orig`
   Original vulnerable source file used as the reduction target.
- `targetfile.c.creduce`
   Final reduced artifact produced by C-Reduce.
- `targetfile.c.lvr`
   Final reduced artifact produced by LVR.
- `targetfile.c.hdd`
   Final reduced artifact produced by the standalone HDD baseline.
- `interestingness.sh`
   Benchmark-specific oracle used for LVR and HDD runs.
- `creduce_interestingness.sh`
   Benchmark-specific oracle used for C-Reduce runs.
- `orig.ast.json`
   Clang AST JSON for the original vulnerable source file.
- `creduce.ast.json`
   Clang AST JSON for the C-Reduce reduction.
- `lvr.ast.json`
   Clang AST JSON for the LVR reduction.

## Purpose of This Repository
This repository is intended to support the evaluation and reproducibility of the LVR study. In particular, it provides the artifacts needed to:
1. inspect the final reductions produced by each reducer.
2. review the benchmark-specific oracle scripts used during reduction.
3. reproduce the AST-based comparison methodology used for precision and recall.
4. compare LVR against C-Reduce and HDD on vulnerability-inducing benchmark cases.

## Benchmarks 
The benchmark cases used in this repository are drawn from the **San2Patch** benchmark and related benchmark infrastructure used in the study. Each CVE directory corresponds to a vulnerability-inducing benchmark instance for which reductions were generated and evaluated.

## Reduction Artifacts 
For each benchmark subject, the following reducers are represented:
- C-Reduce
  Used as the reference reducer in the evaluation.
- LVulnReducer (LVR)
  The HDD-first, LLM-assisted reducer evaluated in the study.
- HDD
  A lightweight hierarchical mechanical reduction baseline used for comparison.

## AST-Based Comparison 
The evaluation of reduction accuracy is based on Clang AST-derived normalized statements rather than raw source-line comparison. The included AST JSON files were generated from the original, C-Reduce, and LVR artifacts and used to compute:
- true-positives,
- false-positives,
- false-negatives,
- precision,
- recall.

This allows LVR to be compared against C-Reduce structurally while reducing sensitivity to formatting, whiespace, brace placement, and other non-semantic source differences.

## Reproducing the AST Data

The AST JSON files in this repository were generated using Clang’s JSON AST dump mode. A representative command is:

```
clang -x c -DHAVE_CONFIG_H -I. -g -O0 -static -Wall -W \
  -Xclang -ast-dump=json -fsyntax-only targetfile.c \
  > output.ast.json
```

The exact compilation flags may vary slightly by benchmark, depending on the project layout and build assumptions required for parsing the target file.

## Notes on Oracle Scripts

The repository includes both:

interestingness.sh for LVR/HDD
creduce_interestingness.sh for C-Reduce

These scripts may differ operationally because C-Reduce reduces files through a temporary working directory, whereas LVR and HDD operate in place. However, they are designed to enforce the same intended vulnerability-preservation criterion for a given benchmark instance.

## Intended Use

This repository is primarily intended for:

artifact inspection,
benchmark comparison,
replication of reduction results,
replication of AST-based accuracy calculations.

It is not intended to serve as a standalone benchmark distribution independent of the original benchmark sources.

## Related Repositories
- LVR source code: https://github.com/MichaelVoskanyan/LVR.git 
- San2Patch benchmark: https://github.com/acorn421/san2patch-benchmark
