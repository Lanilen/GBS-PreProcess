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

**batch_trim.pl**: This program will take a list of fastq reads and barcodes, and wrap around
TrimGalore (which, in turn, is also a wrapper) to remove as much adapter as possible, as
well as the barcodes from the reverse complement of the reads (i.e., the barcode when the
GBS tag is shorter than the read length of the Illumina instrument). Trimming by Illumina
adapter alone can leave the barcode in place, and the barcode might not be picked up if the
reads are then trimmed by cutsite if there's sequencing errors in the multiple cutsites.

The script requires a tab-separated "keyfile" containing the following information:

sampleA_1.fq sampleA_2.fq barcode1 barcode2
sampleB_1.fq sampleB_2.fq barcode1 barcode2
(...)

Headers are optional and only useful for human-reading purpose. The barcodes are read
independently, so they can be the same if the user wants to. The code will create a
cutadapt job for each pair of files with the right barcodes prepared for trimming and
with the Illumina universal adapter.

verify_chimeras: This program takes the output of the previous job and looks for reads
with multiple consecutive cutsites. run the script with no parameters to get the help
on what the software expects, but basically it wants:

**verify_chimeras.pl** reads1 reads2 cutsite cutsite_remnant output_directory

The first two parameters (reads1 and reads2) are self-explanatory, the Illumina reads coming
out of the trimming.

The cutsite is the **remnant** of the enzyme cut site, and takes the form of a regular
expression. So, for example, ApeKI is GC\[AT\]GC and **not** GCWGC. The same goes for the
remnant, it'll be C\{AT\}GC. The software will look for cut sites in a single read,
and remove anything other than the first valid fragment. If there are no cut sites, the
reads are considered valid pairs and output separately. If there are cutsites, the read
is a chimera and it's written as such, with the two ends written as single files because
they really aren't paired, just two random fragments that ended up joined together and
sequenced in one go.

If the reads are missing the initial remnant, they're considered invalid and discarded
completely.
