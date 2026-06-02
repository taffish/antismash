FROM docker.io/antismash/standalone:8.0.4@sha256:d3f390e687cfe02f1387be40256dddbf678e91bc56b96c36da07b56ec8bda29d

ENV LANG=C.UTF-8
ENV MPLCONFIGDIR=/matplotlib
ENV TAFFISH_ENV=TAFFISH
ENV TAFFISH_NAME=antismash

ENTRYPOINT []
CMD ["antismash"]
WORKDIR /root

RUN set -eux; \
    python3 -c 'import antismash; print(antismash.__version__)' | grep -Fx '8.0.4' >/dev/null; \
    antismash --version 2>&1 | grep -F '8.0.4' >/dev/null; \
    antismash --help 2>&1 | grep -F 'usage: antismash' >/dev/null; \
    antismash --help-showall 2>&1 | grep -F -- '--minimal' >/dev/null; \
    test -d /databases; \
    find /databases -mindepth 1 -type f | head -n 1 | grep -q .; \
    diamond version 2>&1 | grep -F 'diamond' >/dev/null; \
    hmmscan -h 2>&1 | grep -F 'HMMER' >/dev/null; \
    hmmsearch -h 2>&1 | grep -F 'HMMER' >/dev/null; \
    prodigal -v 2>&1 | grep -F 'Prodigal' >/dev/null; \
    blastp -version 2>&1 | grep -F 'blastp' >/dev/null
