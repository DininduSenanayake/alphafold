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