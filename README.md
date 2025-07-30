# MEDS KDD 2025 Tutorial
This repository contains relevant files and instructions to reproduce those files for the MEDS KDD 2025
tutorial.

In general, this repository will not be updated after the KDD 2025 conference, and is not expected to be
directly useful for tutorial attendees other than as a source to download files when using the Colab notebooks
containing the interactive tutorial content.

## Files

This repository contains the following files:
  - `README.md`: This file, which provides an overview of the repository and its contents.
  - `task_config.yaml`: The final ACES task configuration file used in the tutorial.
  - `MEDS_data.zip`: A zip file containing a pre-built version of a MEDS dataset and a label file, which is
    used in the tutorial.

## MEDS Dataset Generation

To generate the data in the `MEDS_data.zip` file, you can run the following steps:

### Step 1: Download the MIMIC-IV Demo Dataset

```bash
# Install the ETL package
pip install --quiet MIMIC_IV_MEDS==0.0.6
# Download and extract the MIMIC-IV demo dataset
MEDS_extract-MIMIC_IV root_output_dir=$INTERMEDIATE_DIR do_demo=True do_copy=True

# Move the final MEDS files over.
mv $INTERMEDIATE_DIR/MEDS_cohort/data MEDS_data/
mv $INTERMEDIATE_DIR/MEDS_cohort/metadata MEDS_data/

# Remove the other intermediate files as they are not needed for the tutorial
rm -rf $INTERMEDIATE_DIR

```

### Step 2: Create the Label File

```bash
# Install ACES
pip install --quiet es-aces==0.7.1
aces-cli \
    config_path=task_config.yaml \
    cohort_name="short_LOS" \
    cohort_dir="MEDS_data/labels" \
    data=sharded \
    data.standard=meds \
    data.root=MEDS_data/data \
    data.shard=$(expand_shards train/1 tuning/1 held_out/1) \
    -m

```


### Step 3: Zip the Data

```bash
# Zip the MEDS data directory
zip -r MEDS_data.zip MEDS_data
```
