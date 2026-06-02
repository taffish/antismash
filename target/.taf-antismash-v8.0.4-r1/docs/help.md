taf-antismash 8.0.4-r1

TAFFISH wrapper for antiSMASH 8.0.4, the antibiotics and Secondary
Metabolite Analysis SHell for bacterial and fungal biosynthetic gene
cluster prediction. This app uses the official antiSMASH standalone image,
including the required antiSMASH databases.

Usage:
  taf-antismash --help
  taf-antismash --version
  taf-antismash --compile
  taf-antismash [antismash options] INPUT
  taf-antismash antismash [antismash options] INPUT
  taf-antismash helper-command [args...]

Wrapper options:
  --help       Show this TAFFISH help text.
  --version    Show the TAFFISH package version.
  --compile    Print generated shell code instead of running it.
  --           Stop TAFFISH wrapper option parsing. Usually not needed for
               normal antiSMASH runs; pass antiSMASH options directly.

Default upstream command:
  antismash

Common examples:
  taf-antismash antismash --version
  taf-antismash antismash --help-showall

  taf-antismash \
    --taxon bacteria \
    --output-dir antismash-out \
    genome.gbk

  taf-antismash \
    --taxon bacteria \
    --minimal \
    --output-dir antismash-minimal \
    genome.gbk

  taf-antismash \
    --taxon fungi \
    --output-dir fungismash-out \
    fungal_genome.gbk

  taf-antismash \
    --reuse-results previous-run.json \
    --output-dir regenerated-output

Command mode examples:
  taf-antismash antismash --version
  taf-antismash download-antismash-databases --help
  taf-antismash diamond version
  taf-antismash hmmscan -h
  taf-antismash blastp -version
  taf-antismash python3 -c 'import antismash; print(antismash.__version__)'

Notes:
  The official upstream Docker wrapper uses an input/output/options calling
  style for Docker mount handling. TAFFISH exposes the normal upstream
  antismash CLI directly. Use --output-dir explicitly.

  Normal antiSMASH options can be passed directly, for example
  taf-antismash --taxon bacteria --output-dir out genome.gbk. Use command mode,
  such as taf-antismash antismash --help, for upstream help/version flags that
  share names with TAFFISH wrapper options.

  The official standalone image includes required antiSMASH databases under
  /databases. It is large; upstream documents the standalone download at
  roughly 9 GB. Runtime database download is not needed for normal runs.

  Set --taxon bacteria or --taxon fungi explicitly for reproducible local and
  flow usage. GenBank input is preferred when curated annotations are available;
  FASTA input can be used with antiSMASH gene-finding options.

Platform:
  Native container platform is linux/amd64. Docker and Podman runs request
  --platform linux/amd64 from this app. On arm64 hosts this is emulation, not
  native arm64 support.

Boundaries:
  This app does not choose biological parameters, batch genomes, download input
  genomes, submit jobs to the public antiSMASH web server, or provide workflow
  aggregation. It wraps the upstream local antiSMASH command and the official
  standalone database image.

Docs:
  https://docs.antismash.secondarymetabolites.org/install/
  https://docs.antismash.secondarymetabolites.org/command_line/
  https://github.com/antismash/antismash/tree/8-0-4
