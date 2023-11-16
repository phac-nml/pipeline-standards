# IRIDA Next Nextflow pipeline specification

*This document is in-progress and should be considered as a **DRAFT** that is subject to change at any time.*

This document describes the specification for developing [IRIDA Next][irida-next] Nextflow pipelines. This follows many of the recommendations from the [nfcore pipeline schema][nfcore-pipeline-schema].

A tutorial on developing a pipeline available at [IRIDA Next nf-core pipeline tutorial][pipeline-tutorial].

- [1. Input](#1-input)
  * [1.1. Default samplesheet](#11-default-samplesheet)
  * [1.2. Types of input data](#12-types-of-input-data)
    + [1.2.1. Input files](#121-input-files)
      - [1.2.1.1. Example sample input files](#1211-example-sample-input-files)
      - [1.2.1.2. Example allelic profile input files](#1212-example-allelic-profile-input-files)
    + [1.2.2. Simple input types](#122-simple-input-types)
      - [1.2.2.1. Example simple input types](#1221-example-simple-input-types)
  * [1.3. Modify samplesheet](#13-modify-samplesheet)
    + [1.3.1. Update samplesheet schema specification](#131-update-samplesheet-schema-specification)
      - [1.3.1.1. Select column names from IRIDA Next keywords](#1311-select-column-names-from-irida-next-keywords)
- [2. Parameters](#2-parameters)
- [3. Output](#3-output)
  * [3.1. Files](#31-files)
  * [3.2. IRIDA Next JSON](#32-irida-next-json)
    + [3.2.1. Minimal example](#321-minimal-example)
    + [3.2.2. Complete example](#322-complete-example)
    + [3.2.3. Output files](#323-output-files)
      - [3.2.3.1. global](#3231-global)
      - [3.2.3.2. samples](#3232-samples)
    + [3.2.4. Metadata](#324-metadata)
      - [3.2.4.1. Simplified metadata JSON](#3241-simplified-metadata-json)
- [4. Modules](#4-modules)
  * [4.1. nf-core modules](#41-nf-core-modules)
  * [4.2. Local modules](#42-local-modules)
    + [4.2.1. Module software requirements](#421-module-software-requirements)
- [5. Resource requirements](#5-resource-requirements)
  * [5.1. Process resource label](#51-process-resource-label)
    + [5.1.1. Accepted resource labels](#511-accepted-resource-labels)
  * [5.2. Tuning resource limits with parameters](#52-tuning-resource-limits-with-parameters)
- [6. Testing](#6-testing)
  * [6.1. Nextflow test profile](#61-nextflow-test-profile)
- [7. Executing pipelines](#7-executing-pipelines)
  * [7.1. Standalone execution](#71-standalone-execution)
  * [7.2. Execution via GA4GE WES](#72-execution-via-ga4ge-wes)
- [8. Other resources](#8-other-resources)
- [9. Publishing guidelines](#9-publishing-guidelines)
- [10. Legal](#10-legal)

# 1. Input

Input for pipelines follows the [nfcore parameters][nfcore-parameters] specification. In particular, input data will be passed via a CSV file, where each row represents data that can be processed independently through the pipeline (commonly a sample). The input CSV file is passed using the `--input` parameter to a pipeline (e.g., `--input samples.csv`).

Within Nextflow, this `--input samples.csv` file is converted into a channel where each element within the channel corresponds to a single row of the input CSV file (e.g., each element in the channel contains the sample identifier and the input fastq files). This conversion from the input CSV file to a channel of tuples is performed using the [nf-validation fromSamplesheet][] function.

*Note: Commonly this `--input` CSV file is referred to as a samplesheet since it's often used to store data associated with a single sample, but it could be used to store other types of data as well.*

## 1.1. Default samplesheet

The default samplesheet looks like:

| sample  | fastq_1      | fastq_2      |
|---------|--------------|--------------|
| SampleA | file_1.fq.gz | file_2.fq.gz |

## 1.2. Types of input data

There are two types of input data: **files** and **simple**.

### 1.2.1. Input files

An input file consists of some data stored within a file external to the CSV file passed to `--input`. Commonly this would be sequence reads (and would be indicated with the `fastq_1` and `fastq_2` columns in the CSV file above). However, this could be any data intended to be delivered to pipeline steps via a channel (e.g., `bam` files of aligned reads, `fasta` files of assembled genomes, `csv` files of allelic profiles).

#### 1.2.1.1. Example sample input files

| sample  | fastq_1 **(an input file)** | fastq_2 **(another input file)** | assembly **(another input file)** |
|---------|--------------|--------------|----|
| SampleA | file_1.fq.gz | file_2.fq.gz | |
| SampleB | | | assembly.fa.gz |

#### 1.2.1.2. Example allelic profile input files

| profile_id | scheme    | alleles **(input file)** |
|------------|-----------|--------------------------|
| ProfilesA  | senterica | alleles.tsv.gz           |

### 1.2.2. Simple input types

A simple input type is something that can be represented within a cell in a CSV file without reference to some external file (e.g., a Number or a String). In integration with IRIDA Next, these will often be derived from contextual metadata of a sample (e.g., Organism) or will be the sample identifier.

#### 1.2.2.1. Example simple input types

| sample **(simple input)**  | organism **(simple input)** | fastq_1      | fastq_2      |
|----------------------------|-----------------------------|--------------|--------------|
| SampleA                    | Salmonella enterica         | file_1.fq.gz | file_2.fq.gz |

## 1.3. Modify samplesheet

In order to modify the samplesheet structure there are two main tasks to complete.

### 1.3.1. Update samplesheet schema specification

The [nf-validation samplesheet schema specification][nf-validation samplesheet] defines the structure of the input samplesheet for a pipeline. This consists of a JSON schema file and can be modified to add different columns to the samplesheet for different types of input. This file also accepts the definition of rules for validating the data passed within the samplesheet (e.g., if a column requires only numeric data, this can be defined within the JSON schema).

By default, the samplesheet schema assumes you have three input columns: `sample`, `fastq_1`, and `fastq_2`. Single-end data is handled by leaving `fastq_2` blank.

For custom types of data for a pipeline, please modify this JSON schema by modifying the `assets/schema_input.json` file. Also, make sure your pipeline is configured to use [nf-validation fromSamplesheet][nf-validation fromsamplesheet] to create a channel of input data for your pipeline from an input CSV file that follows this JSON schema.

#### 1.3.1.1. Select column names from IRIDA Next keywords

In order to select data from IRIDA Next, please use one of the following keywords as column names in the `--input` CSV file.

* **sample**: The IRIDA Next sample identifier.
* **fastq_1**: The first of paired-end Illumina fastq files (or a single-end Illumina fastq file).
* **fastq_2**: The second of paired-end Illumina fastq files (leave empty if using single-end data).
* **assembly_fasta**: The assembled genome (in FASTA format).

For an idea of a more advanced method of selecting data from IRIDA Next, please see [IRIDA Next samplesheet ideas documentation][iridanext-samplesheet].

# 2. Parameters

Parameters within a pipeline are defined in the `nextflow.config` file (under the `params` scope), and then the `nextflow_schema.json` file is updated to contain the parameters by running `nf-core schema build`. This is described in the [nf-core contributing to pipelines][nf-core-contributing-to-pipelines] documentation.

As an example, the following is a subset of parameters from the [iridanext-example-nf][] pipeline.

```
params {
    input                      = null
    genome                     = 'hg38'
    igenomes_base              = 's3://ngi-igenomes/igenomes'
    igenomes_ignore            = false
}
```

The full set of parameters can be found in <https://github.com/phac-nml/iridanext-example-nf/blob/main/nextflow.config>.

# 3. Output

## 3.1. Files

Output files will be placed into a directory specified by the `--outdir` parameter. For examples given below we assume `--outdir output` was used.

## 3.2. IRIDA Next JSON

An `output/iridanext.output.json.gz` file will be written, which specifies the files and metadata to store into IRIDA Next.

### 3.2.1. Minimal example

A minimal example of the `iridanext.output.json.gz` is:

**output/iridanext.output.json.gz**:
```json
{
    "files": {
        "global": [],
        "samples": {}
    },
    "metadata": {
        "samples": {}
    }
}
```

This describes the minimal keys and structure for the JSON file.

### 3.2.2. Complete example

A complete example of this file with more data filled in is as below.

**output/iridanext.output.json.gz**:
```json
{
    "files": {
        "global": [
            {"path": "summary.txt"}
        ],
        "samples": {
            "SampleA": [
                {"path": "assembly/assembly.fa.gz"},
                {"path": "assembly/annotation.gbk.gz"}
            ]
        }
    },
    "metadata": {
        "samples": {
            "SampleA": {
                "organism": "Salmonella enterica",
                "amr": [
                    {"gene": "x", "start": 1234, "end": 5678},
                    {"gene": "y", "start": 1, "end": 2}
                ]
            }
        }
    }
}
```

### 3.2.3. Output files

The `files` section lists the files to store within IRIDA Next (files not listed here will not be stored). This consists of two different sections:

#### 3.2.3.1. global

The `global` section defines a list of files associated with the pipeline globally. Each file entry is a JSON object with a single key `path` defining the path to the file (relative to the `output` directory). More keys may be added in the future.

```json
"global": [
    {"path": "summary.txt"}
]
```

#### 3.2.3.2. samples

The `samples` section defines a list of files to be associated with each sample individual (e.g., genome assemblies). Each sample identifier should correspond to the same identifer specified in the `--input` samplesheet. Each file entry is a JSON object with a single key `path` defining the path to the file (relative to the `output` directory).

```json
"samples": {
    "SampleA": [
        {"path": "assembly/assembly.fa.gz"}
    ]
}
```

### 3.2.4. Metadata

The `metadata` section contains structured data to load within the IRIDA Next database. Currently there is only one type of metadata, `samples`.

The `samples` metadata object consists of keys corresponding to sample identifiers used in the original input samplesheet file. The values consist of structured data defining the metadata to store within IRIDA Next.

```json
"metadata": {
    "samples": {
        "SampleA": {
            "organism": "Salmonella enterica",
            "amr": [
                {"gene": "x", "start": 1234, "end": 5678},
                {"gene": "y", "start": 1, "end": 2}
            ]
        }
    }
}
```

#### 3.2.4.1. Simplified metadata JSON

The simplified metadata JSON consists of only a single level of key-value pairs under each `sample` in the metadata object. This will be used to simplify integration of metadata with IRIDA Next prior to support for more complicated data structures.

The below is the equivalent simplified JSON from the version defined in [3.2.4. Metadata](#324-metadata).

```json
"metadata": {
    "samples": {
        "SampleA": {
            "organism": "Salmonella enterica",

            "amr.1.gene": "x",
            "amr.1.start": 1234,
            "amr.1.end": 5678,
            
            "amr.2.gene": "y",
            "amr.2.start": 1,
            "amr.2.end": 2
        }
    }
}
```

# 4. Modules

[Modules in Nextflow][nextflow-modules] are scripts that can contain functions, processes, or workflows.

## 4.1. nf-core modules

Nf-core contains a number of modules for processes to execute different bioinformatics tools ([nf-core modules][]). Where possible, please use nf-core modules within your pipeline. These can be installed vi running the following command from the [nf-core tools][nf-core-modules-install].

```bash
nf-core modules install [NAME]
```

## 4.2. Local modules

If it is not possible to use existing nf-core modules, you can create your own modules local to your pipeline in the `modules/local/` directory. Please see the [nf-core contributing modules][nf-core-contributing-modules] documentation for expectations on how modules should behave. Local modules won't need to follow all of these guidelines (such as uploading to the list of nf-core approved modules), but the behaviour of inputs, parameters, and outputs of a process should be followed as closely as possible.

### 4.2.1. Module software requirements

Each module should define its software requirements as a docker/singularity container. This requires using the `container` keyword in the process. For example:

```
container "${ workflow.containerEngine == 'singularity' && !task.ext.singularity_pull_docker_container ?
    'https://depot.galaxyproject.org/singularity/fastqc:0.11.9--0' :
    'biocontainers/fastqc:0.11.9--0' }"
```

For more information, see the [Nextflow containers][] documentation and the [nf-core modules software requirements][] guide.

# 5. Resource requirements

To define computational resource requirements for each process, we will follow the [nf-core resource][nf-core-module-resource] standards as much as possible, where resources are adjusted by a `label` in each `process` of a pipeline. The pipeline developer will be responsible for setting an appropriate `label` in each process of the NextFlow pipeline to appropriatly define required resources. In addition, the developer is responsible for tuning any resources defined in the `config/base.config` file, as described in the [nf-core tuning workflow resources][] documentation.

## 5.1. Process resource label

The pipeline developer will add a `label` to each `process` of a NextFlow pipeline to adjust resources. For example:

```
process name {
    label 'process_single'

    // ...
}
```

### 5.1.1. Accepted resource labels

The following labels will be accepted:

* `proccess_single`
* `process_low`
* `process_medium`
* `process_high`
* `process_very_high`: This label is an addition over those provided by nf-core to be used for situations where a process needs a lot of resources beyond `process_high`.

## 5.2. Tuning resource limits with parameters

Nf-core provides the capability to adjust the maximum resources given to processes in a pipeline using parameters: `--max_cpus`, `--max_memory`, and `--max_time`. Pipeline developers will be responsible for making sure thse parameters are available in the `nextflow.config` file. See the [nf-core max resources][] documentation for more details.

# 6. Testing

Nextflow pipelines should include test suites, linting, and execution of these tests automatically on merging of new code (i.e., continuous integration). These files should be included automatically if the pipeline is generated using the [nf-core create][nf-core-create] command. A description of these different tests are included in the [nf-core contributing][nf-core-contributing-github-actions] documentation.

## 6.1. Nextflow test profile

In particular, a `test` profile should be setup in Nextflow so that the pipeline can be executed with `nextflow [NAME] -profile test,docker`.

Parameters for the `test` profile should be specified in the `conf/test.config` file. This can reference data stored elsewhere (e.g., on GitHub). For example, see the `test.config` for the IRIDA Next sample pipeline <https://github.com/phac-nml/iridanext-example-nf/blob/main/conf/test.config>.

# 7. Executing pipelines

## 7.1. Standalone execution

Pipelines should be configured so that they can be executed by:

```bash
nextflow run [NAME] --input inputsheet.csv --outdir output [OTHER PARAMETERS]
```

Where `inputsheet.csv` is the CSV file containing samples (or other input data) and `output` is the directory containing output data. Other parameters can be included, though reasonable defaults should be set so the number of required parameters is as minimal as possible.

## 7.2. Execution via GA4GE WES

[IRIDA Next][irida-next] will make use of the [GA4GH Workflow Execution Service][ga4gh-wes] API for executing pipelines. This will require making a `POST` request to **RunWorkflow** with JSON that looks similar to the following:

```json
{
    "workflow_params": {
        "--input": "https://url-to-input.csv",
        "-r": "REVISION",
        "[PARAMETERS]": "[PARAMETER VALUES]"
    },
    "workflow_type": "DSL2",
    "workflow_type_version": "22.10.7",
    "tags": {
        "createdBy": "Test",
        "group": "Test"
    },
    "workflow_engine_parameters": {
        "engine": "nextflow",
        "execute_loc": "azure"
    },
    "workflow_url": "https://github.com/phac-nml/iridanext-example-nf",
    "workflow_attachment": ""
}
```

Here, parameters are specified by key/value pairs under `workflow_params` and the location of the workflow code is given in `workflow_url`. See [WES RunWorkflow][wes-run-workflow] for more details.

# 8. Other resources

* [nfcore pipeline schema][nfcore-pipeline-schema]
* [nfcore parameters][nfcore-parameters]
* [GA4GH Workflow Execution Service][ga4gh-wes]

# 9. Publishing guidelines

Our intention is to follow, as much as possible, the standards and practices set out by nf-core. However, we leave it as optional to actually publish pipelines/modules/subworkflows with the official nf-core repositories. We would encourage this where it makes sense in order to support re-use and giving back to the community (please refer to the [nf-core publishing requirements][] for the guidelines in this case). However, it is perfectly acceptible to publish pipelines/modules/subworkflows in separate Git repositories outside of nf-core. Please see the [if the guidelines don't fit][nf-core-external-development] and [using nf-core components outside of nf-core][nf-core-outside-nf-core] sections of the nf-core documentation for more information on this scenario and other locations to list your pipeline.

# 10. Legal

Copyright 2023 Government of Canada

Licensed under the Apache License, Version 2.0 (the "License"); you may not use
this work except in compliance with the License. You may obtain a copy of the
License at:

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software distributed
under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR
CONDITIONS OF ANY KIND, either express or implied. See the License for the
specific language governing permissions and limitations under the License.

[nfcore-pipeline-schema]: https://nf-co.re/tools/#pipeline-schema
[irida-next]: https://github.com/phac-nml/irida-next
[nf-core]: https://nf-co.re/
[nf-core-create]: https://nf-co.re/tools#creating-a-new-pipeline
[nf-core-contributing-to-pipelines]: https://nf-co.re/docs/contributing/contributing_to_pipelines#default-values
[wes-run-workflow]: https://ga4gh.github.io/workflow-execution-service-schemas/docs/#tag/Workflow-Runs/operation/RunWorkflow
[nf-core-modules-install]: https://nf-co.re/tools#install-modules-in-a-pipeline
[nf-core modules]: https://nf-co.re/modules
[nf-core-module-resource]: https://nf-co.re/docs/contributing/modules#resource-requirements
[nf-core modules software requirements]: https://nf-co.re/docs/contributing/modules#software-requirements
[Nextflow containers]: https://www.nextflow.io/docs/latest/container.html
[nextflow-modules]: https://www.nextflow.io/docs/latest/module.html
[iridanext-example-nf]: https://github.com/phac-nml/iridanext-example-nf
[nfcore-parameters]: https://nf-co.re/docs/contributing/guidelines/requirements/parameters
[nf-core-contributing-github-actions]: https://nf-co.re/docs/contributing/tutorials/nf_core_contributing_overview#github-actions-workflows
[nf-core max resources]: https://nf-co.re/docs/usage/configuration#max-resources
[nf-core tuning workflow resources]: https://nf-co.re/docs/usage/configuration#tuning-workflow-resources
[nf-core-contributing-modules]: https://nf-co.re/docs/contributing/modules
[ga4gh-wes]: https://ga4gh.github.io/workflow-execution-service-schemas/docs/
[nf-core publishing requirements]: https://nf-co.re/docs/contributing/guidelines#requirements
[nf-core-external-development]: https://nf-co.re/docs/contributing/guidelines#if-the-guidelines-dont-fit
[nf-core-outside-nf-core]: https://nf-co.re/docs/contributing/tutorials/unofficial_pipelines
[nf-validation samplesheet]: https://nextflow-io.github.io/nf-validation/nextflow_schema/sample_sheet_schema_specification/
[nf-validation fromSamplesheet]: https://nextflow-io.github.io/nf-validation/samplesheets/fromSamplesheet/
[iridanext-samplesheet]: iridanext-samplesheet.md
[pipeline-tutorial]: https://github.com/apetkau/nf-core-assemblyexample
