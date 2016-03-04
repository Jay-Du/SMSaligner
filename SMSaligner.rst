SMSaligner
============

Introduction
----------------------

Usage
~~~~~~~~~~

SMSSligner provides two models: ref. genome aligner and pairwise aligner
The first finds some seed clustering regions of reference sequence.
The second selects the beginning pair-postion with higher score for local alignment


Advantages
~~~~~~~~~~~~

**a. ref. genome aligner**


* Speed of ref. genome aligner

  For small simulated E.coli and Yeast datesets, In real human dataset, our algorithm is 5.4 times faster than BWA-mem, 70.3 and 109.5 times faster than BLASR, meanwhile, 70.3 and 79.1 times faster than BWA-mem.
For big simulated human dataset, in real human dataset, our algorithm is 5.4 times faster than BWA-mem, 85.5 and 33.8 times faster than BLASR and BWA-mem.
In real human dataset, our algorithm is 5.4 times faster than BWA-mem, at least 40 times faster than BLASR.
In the first four datasets was at least 20 times faster than BWA-mem. In real human dataset, our algorithm is 5.4 times faster than BWA-mem.

* Sensitivity, Correctness and scalability of ref. genome aligner

  Sensitivity and correctness of SMSAligner is higher than BLASR and BWA-mem. For simulated E.coli, Yeast datasets and human dataset.

**b. pairwise aligner**

* Speed of pairwise aligner

  *Comparing SMSAligner with Daligner, MHAP and BLASR*

  SMSAligner is 10~20 times faster than BLASR with two model (fast and sensitivty model) in three simulation datasets and it is 1.2~5 times faster than MHAP in small E.coli and Yeast simulation datasets. In Human chr1 dataset (5x), SMSAligner is 8~20 times faster than other algorithm and it can perform better in the complexity structure datasets.
In Yeast, A.Thaliana, D.Melanogaster and Human real datasets, SMSAligner is 5~30 times faster than the other three algorithms and its consuming-memory is much lower than the other algorithms.

* Sensitivity, Correctness and scalability of pairwise aligner

  Sensitivity of SMSAligner is available at about 70~80% and it is much higher than BLASR_S and MHAP in three simulated datasets; meanwhile, correctness of SMSAligner is available at about 90% and it is higher that MHAP and BLASR. All result show that SMSAligner can get better balance of sensitivity and correctness.

Instructions for use
~~~~~~~~~~~~~~~~~~~~~~

**a. ref. genome aligner**


 ``$ Reference -d [fastq file name] –r [fasta file name] –w [work folder] –t [number of CPUs] –o [output file name]``

*Options*

::

  -d [dataset]: specify deep sequencing dataset file name (FASTQ format)

  -r [ref]: specify reference genome file name (FASTA format).Note: Characters other than letters, digits and “-“ are not allowed in the file name and its path.

  -o [output]: specify output file name

  -t [number]: parallel CPU core number in the processing. This number should not exceed the logical CPU core numbers of all machines involved in the calculation

  -w [folder]: work folder where the temporary file was stored. The folder should be enough large storage space. Note: Please be sure that you have enough space in the temporary folder! You need at least the free space twice as the reference sequence file and the dataset file

**b. pairwise aligner**


 ``$ SMSpairwise –d [fastq file name]–w [work folder] –t [number of CPUs] –o [output file name]``

*Options*

::

  -d [dataset]: specify deep sequencing dataset file name (FASTQ format)

  -o [output]: specify output file name

  -t [number]: parallel CPU core number in the processing. This number should not exceed the logical CPU core numbers of all machines involved in the calculation

  -w [folder]: work folder where the temporary file was stored. The folder should be enough large storage space. 
Note: Please be sure that you have enough space in the temporary folder! You need at least the free space twice as the reference sequence file and the dataset file


Output
~~~~~~~~~~~~~~~

**a. ref. genome aligner**


Each mappable sequence will be outputed as three lines in the result text file. Each line contains multiple fields separated by tab.

Line 1: [Read name] [Chromosome name in Ref. sequence] [Forward/Reverse complement strand] [Number of errors (here always equals to 10) ] [Position in the sequence ] [Position in the chromosome]

Line 2: The mapped sequence of this read.

Line 3: The mapped sequence of the Ref. sequence.

Here is an example:

::

  1	gi|556503834|ref|NC_000913.3|	F	10	2	58	1988134	1988197

  AAT-AGCGCCTGCCAGGCG-TCTTTT--CCGGCCATTGT-CGCAG--CACTGTAACGCGTAAAA

  AATTAGCGCCTGCCAGGCGGTCTTTTTTCCGGCCATTGTTCGCAGGG-ACTGTAACGCGTAAAA


The read sequence name is named “1” followed by the Chromosome name in Ref. sequence. This read is mapped to a forward strand “F” (when mapped to reverse complement strand, it will be designated with “R”), to the chromosome at the position started with 2 and end with 58 in this read, while started with 1988134 and end with 1988197 in the Ref. sequence . The read sequence is always given in 5’-3’ direction, exactlythe same as it is given in the input FASTQ file.

Note that the position is zero-based, i.e. if a read is mapped to the 5’-end of the reference sequence, its position is 0. Since only the forward strand of a reference sequence is given in the databases, all the positions are numbered in the forward strand. For a read mapped to the forward strand, the first position is the 5’-end nucleotide of the read (see the illustration below). For a read mapped to the reverse strand, the mapped position is the 3’-end nucleotide at the forward strand, i.e., the nucleotide positions of reads mapped to the reverse strand will be in 3’-5’. The red flags in the scheme below mark the mapped positions.



**b. pairwise aligner**


Pairwise outputs overlaps in a format similar to BLASR’s M4 format. Example output:

[A ID] [B ID] [Jaccard score] [# shared min-mers] [0=A fwd, 1=A rc] [A start] [A end] [A length] [0=B fwd, 1=B rc] [B start] [B end] [B length]

An example of output from a small dataset is below:

::

  155 11 87.83225 206 0 69 1693 1704 0 1208 2831 5871

  155 15 85.08692 163 0 16 1041 1704 1 67 1088 2935

  155 27 87.11507 159 0 455 1678 1704 0 0 1225 1862


In this case sequence 155 overlaps 11, 15, and 27.


Notices
~~~~~~~~~~

**a. ref. genome aligner**


*Memory consumption:*

::

  Maximum of fully indexing = genome size*4（or 8）

  Every kernel compressing index = genome size*0.1*(kernel number)

  Local sequence alignment consumption and other memory consumption = 100 M * (kernel number) + 1G


**b. pairwise aligner**


*Memory consumption:*

All of the data is split into many files, and each file is about 2G. Thus, genomesize equals to 2G. Other is similar to the above. Then,

::

  Maximum of fully indexing = genome size*4（or 8）= 2G*4 (or 8) =8 (or 16) G

  Every kernel compressing index = genome size*0.1*(kernel number) = 0.2*(kernel number)

  Local sequence alignment consumption and other memory consumption = 100 M * (kernel number) + 1G

