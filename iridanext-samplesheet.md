# IRIDA Next Samplesheet idea

The following is a description of a more complex way to match up columns in a pipeline samplesheet with data from IRIDA Next.

# 1. Match CSV columns to IRIDA Next data

The default `--input` CSV file consists of the columns `sample`, `fastq_1`, and `fastq_2`. These will be automatically matched up to a sample identifier, and paired-end (or single-end) fastq files stored within IRIDA Next.

In order to match up additional information, you will need to define a `assets/irida-next.json` specification file that matches `--input` CSV file columns to keywords in IRIDA Next for selecting data. For example:

**assets/irida-next.json**
```json
{
    "specification" : {
        "version": "1.0.0"
    },

    "inputs": {
        "input": {
            "sample": "sample_id",
            "organism": "organism_name",
            "assembly": "assembly_fasta",
        }
    }
}
```

## 1.1. Specification section

The `specification` section defines information about the IRIDA Next specification. Here, the only key available is the version (included so that this file can be expanded later).

```json
{
    "specification" : {
        "version": "1.0.0"
    }
}
```

## 1.2. Inputs section

The `inputs` section defines how to map column names in the input CSV file to specific types of data in IRIDA Next.

```json
{
    "inputs": {
        "input": {
            "sample": "sample_id",
            "fastq_1": "illumina_fastq_1",
            "fastq_2": "illumina_fastq_2",
            "nanopore": "nanopore_fastq",
            "assembly": "assembly_fasta",
        }
    }
}
```

Here, only one type of key is available, `input`, which corresponds to the `--input` CSV file. This consists of key-value pairs that define data to select, in the form of `"[SAMPLESHEET COLUMN NAME]": "[IRIDA NEXT DATA]"`.

The `"[SAMPLESHEET COLUMN NAME]"` defines the name of the column in the samplesheet CSV file.

| sample  | fastq_1      | fastq_2      |
|---------|--------------|--------------|
| SampleA | file_1.fq.gz | file_2.fq.gz |

The `"[IRIDA NEXT DATA]"` defines a keyword used to select data from IRIDA Next.
