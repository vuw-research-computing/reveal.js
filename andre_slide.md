# Rebuilding an HPC for fun and profit - more fun really.

----

![Image of Ansible log](images/Ansible_logo.png) <!-- .element width="20%"  -->
![Image of Git logo](images/Git-Logo.png)  <!-- .element width="20%" -->
![Image of Slurm logo](images/Slurm_logo.png)  <!-- .element width="20%" -->

----

* Testing at small scale
    * Frugal IT
    * 500 cores and 4 high memory nodes (512GB) 

----

Frakencluster

![Image of Ansible log](images/cluster.jpg) <!-- .element width="20%"  -->

1752 cores and 6 high mem nodes

Note:
This will only display in the notes window.
* Currently 1752 cores and 6 high mem nodes
* Dangers of corporate IT designed datacenter - power!



----

<section style="text-align: left;"> 

## Singularity/MaxBin2 Example
In a sensible location, either in your home directory or on the scratch:

Get the maxbin2 container, there are a few places to get this, but will get the bioconda container as it is more recent than the one referenced on the official maxbin site.

```bash
module load module load singularity
singularity pull docker://quay.io/biocontainers/maxbin2:2.2.6--h14c3975_0
mv maxbin2_2.2.6--h14c3975_0.sif maxbin2_2.2.6.sif #rename for convenience
```

Download some test data

```bash
mkdir rawdata
curl https://downloads.jbei.org/data/microbial_communities/MaxBin/getfile.php?20x.scaffold > rawdata/20x.scaffold
curl https://downloads.jbei.org/data/microbial_communities/MaxBin/getfile.php?20x.abund > rawdata/20x.abund
```

Create an output data location
```bash
mkdir output
```

----

<section style="text-align: left;"> 
Create a submit script using singularity on the cluster

singularity_submit.sh
```bash
#!/bin/bash

#SBATCH --job-name=maxbin2_test
#SBATCH -o sing_test.out
#SBATCH -e sing_test.err
#SBATCH --time=00:10:00
#SBATCH --partition=parallel
#SBATCH --ntasks=4
#SBATCH --mem=4G

module load singularity

singularity exec maxbin2_2.2.6.sif run_MaxBin.pl -contig rawdata/20x.scaffold -abund rawdata/20x.abund -out output/20x.out -thread 4 
```

----