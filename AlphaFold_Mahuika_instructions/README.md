### AlphaFold Databases 

There are eight reference databases (parameters where all of them are downloaded, verified and stored in `/opt/nesi/db/alphafold_db` . 

```bash
/opt/nesi/db/alphafold_db/
├── bfd
├── mgnify
├── params
├── pdb70
├── pdb_mmcif
├── small_bfd
├── uniclust30
└── uniref90
```

### Singularity container

We prepared a Singularity container image based on the official Dockerfile with some midifications. Image (.*simg*) and the corresponding defintion file (*.def*) are stored in `/opt/nesi/containers/AlphaFold/2021-08-14`

### Example Slurm script

>Input fasta used in following example and subseuqent benchmarking is 3RGK (https://www.rcsb.org/structure/3rgk). 


```bash
#!/bin/bash -e

#SBATCH --account           nesi12345
#SBATCH --job-name          alphafold2
#SBATCH --mem               20G
#SBATCH --cpus-per-task     6
#SBATCH --gpus-per-node     P100:1
#SBATCH --time              01:20:00
#SBATCH --output            slurmout.%j.out

module purge
module load cuDNN/8.1.1.33-CUDA-11.2.0 Singularity/3.8.0

image=/opt/nesi/containers/AlphaFold/2021-08-14
database=/opt/nesi/db/alphafold_db

export SINGULARITY_BIND="$PWD:/etc,$image,/path/to/input/data:/var/inputdata,/path/to/outputs:/var/outputdata,$database:/db"

singularity run --pwd /app/alphafold --nv $image/alphafold2_v2.simg python /app/alphafold/run_alphafold.py \
--fasta_paths=/var/inputdata/3RGK.fasta \
--output_dir=/var/outputdata \
--model_names=model_1 \
--preset=casp14 \
--max_template_date=2020-05-14 \
--data_dir=/db \
--uniref90_database_path=/db/uniref90/uniref90.fasta \
--mgnify_database_path=/db/mgnify/mgy_clusters_2018_12.fa \
--uniclust30_database_path=/db/uniclust30/uniclust30_2018_08/uniclust30_2018_08 \
--bfd_database_path=/db/bfd/bfd_metaclust_clu_complete_id30_c90_final_seq.sorted_opt \
--pdb70_database_path=/db/pdb70/pdb70 \
--template_mmcif_dir=/db/pdb_mmcif/mmcif_files \
--obsolete_pdbs_path=/db/pdb_mmcif/obsolete.dat
```

#### Explanation of Slurm variables and  Singularity flags

1. Values for  `--mem` , `--cpus-per-task` and `--time` Slumr variables are for *3RGK.fasta*. Adjust them accordingly
2. We have tested this on both P100 and A100 GPUs where the runtimes were identical. Therefore, the above example was set to former via `P100:1`
3. The `--nv` flag enables GPU support.
4. `--pwd /app/alphafold` is to workaround this [existing issue](https://github.com/deepmind/alphafold/issues/32)