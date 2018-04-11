# GBS-PreProcess
Scripts to pre-process Genotyping-by-Sequencing data (GBS) before analysis with STACKS or,
potentially, any other GBS pipeline. The scripts will trim reads down by cut site and the
universal illumina adapter, taking into account the enzyme used and the barcodes.

Prerrequisites:
    1. Perl. The scripts use simple regexp perl, so it should be compatible with even ancient perl versions.
    2. TrimGalore.

Other trimming software can be used, but the code will have to be modified. TrimGalore is
useful because it allows for the trimming based on different adapters for each end (which
the scripts take advantage of by using the adapters as barcodes), and is fast.

The scripts should be run in order:


