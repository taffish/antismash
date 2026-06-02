# taf-antismash

TAFFISH wrapper for [antiSMASH](https://antismash.secondarymetabolites.org/),
the antibiotics and Secondary Metabolite Analysis SHell for bacterial and
fungal secondary metabolite biosynthetic gene cluster prediction.

This app packages antiSMASH `8.0.4-r1` using the official
`antismash/standalone:8.0.4` Docker image as the base image. That upstream
standalone image includes the required antiSMASH databases, so normal local
runs do not need a separate database download. The tradeoff is size: upstream
documents the standalone image as roughly a 9 GB download.

## Package Identity

- name: `antismash`
- command: `taf-antismash`
- version: `8.0.4-r1`
- kind: `tool`
- image: `ghcr.io/taffish/antismash:8.0.4-r1`
- upstream: antiSMASH tag `8-0-4`
- runtime version: antiSMASH `8.0.4`
- upstream Docker base: `antismash/standalone:8.0.4`
- upstream Docker digest: `sha256:d3f390e687cfe02f1387be40256dddbf678e91bc56b96c36da07b56ec8bda29d`
- default command: `antismash`
- native platform: `linux/amd64`

## Install

```sh
taf install antismash
```

Install the exact release:

```sh
taf install antismash 8.0.4-r1
```

For local testing before this app is published to the public index:

```sh
taf install --from .
```

## Basic Usage

Show TAFFISH wrapper help and version:

```sh
taf-antismash --help
taf-antismash --version
taf-antismash --compile
```

Show upstream antiSMASH help and version:

```sh
taf-antismash antismash --help
taf-antismash antismash --help-showall
taf-antismash antismash --version
```

Run a default analysis:

```sh
taf-antismash \
  --taxon bacteria \
  --output-dir antismash-out \
  genome.gbk
```

Run a minimal analysis:

```sh
taf-antismash \
  --taxon bacteria \
  --minimal \
  --output-dir antismash-minimal \
  genome.gbk
```

Run with selected extra analyses:

```sh
taf-antismash \
  --taxon bacteria \
  --cb-general \
  --cb-knownclusters \
  --pfam2go \
  --output-dir antismash-clusterblast \
  genome.gbk
```

Regenerate outputs from a previous antiSMASH JSON file:

```sh
taf-antismash \
  --reuse-results previous-run.json \
  --output-dir regenerated-output
```

## Command Mode

`taf-antismash --help` prints this TAFFISH app help. Normal antiSMASH
option-led calls can be passed directly to the default upstream command:

```sh
taf-antismash --minimal --output-dir out genome.gbk
```

Because `command_mode = true`, a first non-option argument is treated as an
executable inside the same container. This exposes helper programs and
dependencies without adding extra wrappers:

```sh
taf-antismash antismash --version
taf-antismash download-antismash-databases --help
taf-antismash diamond version
taf-antismash hmmscan -h
taf-antismash blastp -version
taf-antismash python3 -c 'import antismash; print(antismash.__version__)'
```

Use command mode for upstream `--help` and `--version`, because those names are
also TAFFISH wrapper options:

```sh
taf-antismash antismash --help
taf-antismash antismash --version
```

The official upstream Docker wrapper uses an `input output options...` calling
style to manage Docker mounts. TAFFISH instead exposes the normal upstream
`antismash` command line directly, so use antiSMASH options such as
`--output-dir` yourself.

## Inputs And Outputs

antiSMASH accepts annotated GenBank inputs and FASTA inputs. GenBank is usually
preferred when curated features are available; FASTA input can be used with
antiSMASH gene-finding options. Set `--taxon bacteria` or `--taxon fungi`
explicitly for reproducible local and flow usage.

Typical outputs include:

- JSON results for result reuse and downstream parsing
- HTML reports and static assets when HTML output is enabled
- GenBank/EMBL-style annotated sequence outputs
- module-specific result files depending on enabled analyses

Use `--output-dir DIR` to keep outputs explicit. The wrapper does not create a
TAFFISH-specific output layout.

## Runtime Contents

The image keeps the official antiSMASH standalone runtime intact and only
overrides the Docker entrypoint so TAFFISH can expose the upstream CLI directly.

Bundled software and resources include:

- antiSMASH `8.0.4`
- the standalone antiSMASH database set under `/databases`
- Python 3 and antiSMASH Python modules
- DIAMOND
- HMMER 2/3 tooling used by antiSMASH modules
- Prodigal
- NCBI BLAST+
- FastTree and other helper programs shipped by the official image
- MEME suite components present in the official image

The official antiSMASH documentation describes the standalone image as the
zero-fuss Docker route with required databases included. A lighter upstream
`standalone-lite` route exists, but this TAFFISH app intentionally packages the
full standalone image so core analyses are not blocked by missing databases.

## Platform

The official standalone image is published as `linux/amd64`. This app declares
native support for `linux/amd64` only and asks Docker/Podman to run with
`--platform linux/amd64` from `src/main.taf`. On Apple Silicon or other arm64
hosts, Docker/Podman may run it through amd64 emulation; that is not native
arm64 support. Apptainer behavior depends on site configuration and whether the
host can run amd64 containers.

## Boundaries

This app is a tool wrapper, not a TAFFISH flow. It does not choose taxon,
genome-quality thresholds, clusterblast options, fungal versus bacterial
interpretation, or downstream biological filtering for the user.

This app does not bundle:

- input genomes or example datasets
- MIBiG downloads beyond what the official standalone database set includes
- remote antiSMASH web-service submission
- custom private databases
- workflow-level batching, QC, comparative genomics, or report aggregation

Runtime network access is not required for normal standalone runs. Commands
that explicitly download or update databases, or workflows that fetch input
genomes, require network access and should use a persistent host-mounted
directory.

## License Boundary

The TAFFISH app packaging files are licensed under Apache-2.0. The packaged
upstream antiSMASH software is licensed under AGPL-3.0-or-later. Bundled
databases, third-party command-line tools, models, and data resources keep
their own upstream license terms and notices.

## Citation

For antiSMASH 8, cite:

Blin et al. 2025. antiSMASH 8.0: extended gene cluster detection capabilities
and analyses of chemistry, enzymology, and regulation. Nucleic Acids Research.
DOI: `10.1093/nar/gkaf334`; PMID: `40276974`.

Useful official documentation:

- <https://docs.antismash.secondarymetabolites.org/install/>
- <https://docs.antismash.secondarymetabolites.org/command_line/>
- <https://github.com/antismash/antismash/tree/8-0-4>

## Smoke Coverage

Smoke tests cover:

- wrapper metadata and TAFFISH build parsing
- antiSMASH Python package version and upstream CLI version
- upstream help and full-help option surface
- bundled `/databases` presence and antiSMASH module-data preparation
- key helper executables: DIAMOND, HMMER, Prodigal, and BLAST+
- a tiny offline FASTA run with `--taxon bacteria --minimal`, checking that a
  JSON result file is produced

The smoke run is intentionally small. It does not validate biological accuracy,
large genomes, every optional module, or performance on production datasets.
