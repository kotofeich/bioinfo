<style type="text/css">
.Reference {line-height 1.2; padding-left:4em; text-indent:-2em; padding-right:1em}
</style>

Various small bioinformatics scripts
====================================

### samHeader2bed.pl

Read a SAM header and produce a BED file containing reference sequences
satisfying min/max length criteria.  Useful if you want to do something like
call SNPs in contigs >= 1kb:

    samtools view -H your.bam | samHeader2bed.pl -minlength 1000 - > min1000.bed
    samtools mpileup -l min1000.bed -u -f ref.fa your.bam | bcftools view ...


### pileup2pro.pl

Convert pileup to profile format as used for input to mlRho.  This is a format
in which bases present at each position in a mapping to a reference sequence
are enumerated.  This script simply converts the format, so any filtering on
base/mapping quality, etc. that you may wish to do should be done when
generating the pileup:

    samtools mpileup -q1 -f ref.fa your.bam | pileup2pro.pl > mlRho-input.txt

mlRho (http://guanine.evolbio.mpg.de/mlRho) estimates population genetic
parameters from NGS data sequenced from a diploid genome.

<div class="Reference">

Haubold B, P Pfaffelhuber, and M Lynch. 2010. mlRho - a program for estimating
the population mutation and recombination rates from equenced diploid genomes.
*Molecular Ecology* 19s1:277-284.

Lynch M. 2008. Estimation of nucleotide diversity, disequilibrium coefficients,
and mutation rates from high-coverage genome-sequencing projects. *Molecular
Biology and Evolution* 25:2421-2431. 

</div>

Profile format contains reference names, coordinates and raw numbers of bases:

    >contig_1
    1	0	2	0	0
    2	2	0	0	0
    3	0	2	0	0
    4	2	0	0	0
    5	0	0	0	2
    6	0	0	2	0
    7	2	0	0	0
    8	0	0	0	2
    9	0	0	0	2
    10	2	0	0	0
    ...


### shuffleFastq.pl, deshuffleFastq.pl

Convert FastQ-format files from separate read 1 and read 2 files to interleaved
files, and back.  Input is automatically un-gzipped if required with no
particular filename requirements, and output is gzipped if the filename ends
with `.gz`.  All the gzipping/ungzipping is done in pipes.  With the '`-`' option,
`shuffleFastq.pl` will write to `stdout` and `deshuffleFastq.pl` will read from
`stdin`.

    shuffleFastq.pl  FA.1.fq FA.2.fq FA.i.fq.gz

    deshuffleFastq.pl  FB.i.fq.gz FB.1.fq.gz FB.2.fq.gz

    cat FC.i.fq | deshuffleFastq.pl - FC.1.fq.gz FC.2.fq.gz

etc.  `deshuffleFastq.pl` has a couple other options for filtering reads based
on minimum read length:

    deshuffleFastq.pl -minlen 30 -single FD.se.fq.gz FD.i.fq.gz FD.1.fq.gz FD.2.fq.gz
  
This will only include read pairs in the output where both reads are at least
30bp long.  Any read that meets this criterion but has a mate that does not is
written to `FD.se.fq.gz`.  If the -`single `option is not specified, such reads are
dropped along with their mates.

These were originally built on the the `shuffleSequences_fastq.pl` and
`deshuffleSequences_fastq.pl` scripts distributed with velvet.
