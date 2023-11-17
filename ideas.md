# IRIDA Next pipeline integration ideas

The following are collections of additional ideas for integrating pipelines in the future.

<a href="samplesheets"></a>
# 1. Future IRIDA Next Samplesheets

The following is a description of a more complex way to match up columns in a pipeline samplesheet with data from IRIDA Next.

## 1.1. Match CSV columns to IRIDA Next data

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

### 1.1.1. Specification section

The `specification` section defines information about the IRIDA Next specification. Here, the only key available is the version (included so that this file can be expanded later).

```json
{
    "specification" : {
        "version": "1.0.0"
    }
}
```

### 1.1.2. Inputs section

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

# 2. IRIDA Next Nextflow plugin

In the future, it might be useful to try to move a lot of the IRIDA Next requirements into an `nf-iridanext` [plugin for Nextflow][nextflow-plugin]. These IRIDA Next requirement include specific methods to define input data and outputs (as spelled out in the [Requirements for integrating with IRIDA Next][iridanext-requirements] section). With an IRIDA Next-specific plugin, some of this configuration for integreating pipelines with IRIDA Next could be defined in configuration files. For example:

```
iridanext {
    input_mapping {
        // Maps columns in samplesheet to keywords for selecting data from IRIDA Next
        fastq_1 = "illumina-fastq-1"
        fastq_2 = "illumina-fastq-2"
        organism = "organism_id"
    }
    // Defines name of file IRIDA Next expects
    iridanext_file = "iridanext.output.json.gz"
    language_files_prefix = "messages"
}
```

These configurations could be done independently of pipeline code (e.g., to register a pipeline with IRIDA Next you provide the code repository of a pipeline plus additional IRIDA Next plugin configuration). This could provide a way to minimize the number of changes needed to code for integrating pipelines from nf-core (by consolidating all changes into a separate configuration file specific for IRIDA Next integration).

[nextflow-plugin]: https://www.nextflow.io/docs/latest/plugins.html
[iridanext-requirements]: README.md#13-requirements-for-integrating-with-irida-next
