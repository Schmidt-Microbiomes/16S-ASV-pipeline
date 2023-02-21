# Schmidt Lab 16S ASV pipeline

This repository documents the Schmidt Lab's 16S ASV pipeline.  The pipeline is a derived from the [Schloss MiSeq SOP](https://mothur.org/wiki/miseq_sop/).

## requirements

- Mothur version 1.48
- Python 3
- a set of fastq files from a MiSeq 16S amplicon sequencing project
- a prepared 3-column mothur files file
- a prepared SILVA reference alignment file
- optional: ASV reference fasta file

## How to run the pipeline

Follow roughly these step, adjust to your environment as needed, the below assumes you run a bash shell on a UNIX system:

1. Copy the files of this repository into your working directory.
2. Prepare a mothur 3-column Mothur files file (the one that maps sample names to pairs of fastq files), name it e.g. `foo.files` were foo is the name
   of your project or so.
3. Prepare the SILVA alignment according to [Schloss MiSeq SOP](https://mothur.org/wiki/miseq_sop/) that is get the
   [SILVA reference files](https://mothur.org/wiki/Silva_reference_files) and run the pcr.seqs command as directed.
4. Run the Mothur batch file.  The batch file requires three environment variable set: `NUM_CPUS` is the number of CPUs you want to use for
   multi-processing/multi-threading steps, `MOTHUR_PREFIX` is the name of your files file without the `.files` suffix. `SILVA_NR_PCR_ALIGN` is set to the
   path were your SILVA alignment is located.  The containing directory needs to the writable to you as Mothur will create an .8mer file there.  So we run
   something similar to:
   ```
   export NUM_CPUS=8
   export MOTHUR_PREFIX=foo
   export SILVA_NR_PCR_ALIGN=/path/to/silva_nr_v132.pcr.align
   mothur MiSeq_16S_ASV.mob
   ```
5. After Mothur is done, we run the following:
   ```
   ./ensure-uniqueness foo.asv.fasta foo.asv.asv.list foo.asv.asv.shared
   ./remove-singletons foo.asv.unique.fasta foo.asv.asv.unique.shared
   ./assign-asv-numbers asv-reference.fasta foo.asv.unique.rmsingl.fasta foo.asv.asv.unique.rmsingl.shared
   ```
