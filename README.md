# IRIDA Next Nextflow pipeline specification

_This document is in-progress and should be considered as a **DRAFT** that is subject to change at any time._

This document describes the specification for developing [IRIDA Next][irida-next] Nextflow pipelines.

- [1. Starting development](#1-starting-development)
  - [1.1. Developing a pipeline to contribute to nf-core](#11-developing-a-pipeline-to-contribute-to-nf-core)
  - [1.2. Developing a pipeline which won't be contributed to nf-core](#12-developing-a-pipeline-which-wont-be-contributed-to-nf-core)
    - [1.2.1. Requirements from nf-core](#121-requirements-from-nf-core)
  - [1.3. Requirements for integrating with IRIDA Next](#13-requirements-for-integrating-with-irida-next)
  - [1.4. Other requirements](#14-other-requirements)
- [2. Input](#2-input)
  - [2.1. Default samplesheet](#21-default-samplesheet)
  - [2.2. Types of input data](#22-types-of-input-data)
    - [2.2.1. Sample identifier](#221-sample-identifier)
    - [2.2.2. Input files](#222-input-files)
      - [2.2.2.1. Example sample input files](#2221-example-sample-input-files)
    - [2.2.3. Metadata input types](#223-metadata-input-types)
      - [2.2.3.1. Example metadata input types](#2231-example-metadata-input-types)
  - [2.3. Defining the samplesheet structure for a pipeline](#23-defining-the-samplesheet-structure-for-a-pipeline)
    - [2.3.1. Defining sample identifiers in IRIDA Next](#231-defining-sample-identifiers-in-irida-next)
    - [2.3.2. Defining selection of input files in IRIDA Next](#232-defining-selection-of-input-files-in-irida-next)
      - [2.3.2.1. Example samplesheet and JSON schema for an assembly](#2321-example-samplesheet-and-json-schema-for-an-assembly)
    - [2.3.3. Defining selection of input metadata in IRIDA Next](#233-defining-selection-of-input-metadata-in-irida-next)
      - [2.3.3.1. Example samplesheet and JSON schema for a metadata value](#2331-example-samplesheet-and-json-schema-for-a-metadata-value)
- [3. Parameters](#3-parameters)
- [4. Output](#4-output)
  - [4.1. Files](#41-files)
  - [4.2. IRIDA Next JSON](#42-irida-next-json)
    - [4.2.1. Minimal example](#421-minimal-example)
    - [4.2.2. Complete example](#422-complete-example)
    - [4.2.3. Output files](#423-output-files)
      - [4.2.3.1. global](#4231-global)
      - [4.2.3.2. samples](#4232-samples)
    - [4.2.4. Metadata](#424-metadata)
    - [4.2.5. Creating the IRIDA Next JSON integration file](#425-creating-the-irida-next-json-integration-file)
- [5. Modules](#5-modules)
  - [5.1. nf-core modules](#51-nf-core-modules)
  - [5.2. Local modules](#52-local-modules)
    - [5.2.1. Module software requirements](#521-module-software-requirements)
- [6. Resource requirements](#6-resource-requirements)
  - [6.1. Process resource label](#61-process-resource-label)
    - [6.1.1. Accepted resource labels](#611-accepted-resource-labels)
  - [6.2. Tuning resource limits with parameters](#62-tuning-resource-limits-with-parameters)
- [7. Testing](#7-testing)
  - [7.1. Nextflow test profile](#71-nextflow-test-profile)
  - [7.2. Unit/integration tests](#72-unitintegration-tests)
- [8. Executing pipelines](#8-executing-pipelines)
  - [8.1. Standalone execution](#81-standalone-execution)
  - [8.2. Execution via GA4GE WES](#82-execution-via-ga4ge-wes)
  - [8.3. Running pipelines in a local instance of IRIDA Next](#83-running-pipelines-in-a-local-instance-of-irida-next)
- [9. Resources](#9-resources)
  - [9.1. Pipeline development tutorial](#91-pipeline-development-tutorial)
  - [9.2. List of pipelines](#92-list-of-pipelines)
  - [9.3. Other resources](#93-other-resources)
- [10. Future pipeline integration ideas](#10-future-pipeline-integration-ideas)
- [11. Security practices](#11-security-practices)
  - [11.1. Automated repository scanning (recommendations)](#111-automated-repository-scanning-recommendations)
- [12. Publishing guidelines](#12-publishing-guidelines)
- [13. Legal](#13-legal)

# 1. Starting development

IRIDA Next pipelines attempt to follow as much as possible the [nf-core][] guidelines for development pipelines. As a preliminatry first step prior to developing pipelines in IRIDA Next, **please read through the following documentation:**

- [Nf-core guidelines][nf-core-guidelines]
- [Nf-core adding a new pipeline][nf-core-add-pipeline]

While we do follow nf-core as closely as possible, there are a number of differences or additions we make for integrating pipelines into IRIDA Next. Hence, some of the nf-core requirements may not apply if you do not intend to contribute the pipeline to nf-core.

## 1.1. Developing a pipeline to contribute to nf-core

If you intend to develop a pipeline to contribute to nf-core, please follow all the [nf-core requirements][nf-core-requirements] in addition to the requirements listed in this guide.

## 1.2. Developing a pipeline which won't be contributed to nf-core

If, instead, the pipeline won't be contributed to nf-core, then only the following set of nf-core requirements would need to be followed, along with the IRIDA Next requirements sections below.

### 1.2.1. Requirements from nf-core

- [ ] Nextflow: Workflows must be built using Nextflow.
- [ ] [Workflow name](https://nf-co.re/docs/contributing/guidelines/requirements/workflow_name): Names should be lower case and without punctuation.
- [ ] [Use the template](https://nf-co.re/docs/contributing/guidelines/requirements/use_the_template): All nf-core pipelines must be built using the nf-core template.
- [ ] [Software license](https://nf-co.re/docs/contributing/guidelines/requirements/mit_license): Pipelines must open source, released with the MIT license.
- [ ] [Docker support](https://nf-co.re/docs/contributing/guidelines/requirements/docker): Software must be bundled using Docker and versioned.
- [ ] [Continuous integration testing](https://nf-co.re/docs/contributing/guidelines/requirements/ci_testing): Pipelines must run CI tests.
- [ ] [Semantic versioning](https://nf-co.re/docs/contributing/guidelines/requirements/semantic_versioning): Pipelines must use stable release tags.
- [ ] [Standardised parameters](https://nf-co.re/docs/contributing/guidelines/requirements/parameters): Strive to have standardised usage.
- [ ] [Single command](https://nf-co.re/docs/contributing/guidelines/requirements/single_command): Pipelines should run in a single command.
- [ ] [Keywords](https://nf-co.re/docs/contributing/guidelines/requirements/keywords): Excellent documentation and GitHub repository keywords.
- [ ] [Pass lint tests](https://nf-co.re/docs/contributing/guidelines/requirements/linting): The pipeline must not have any failures in the nf-core lint tests.
  - _Note: It is possible to ignore some of the default nf-core linting with a [lint configuration file](https://nf-co.re/tools#linting-config)._
- [ ] [Credits and Acknowledgements](https://nf-co.re/docs/contributing/guidelines/requirements/acknowledgements): Pipelines must properly acknowledge prior work.
- [ ] [Minimum inputs](https://nf-co.re/docs/contributing/guidelines/requirements/minimum_inputs): Pipelines should be able to run with as little input as possible.

The following are modified nf-core requirements:

- [ ] [Bundled documentation](https://nf-co.re/docs/contributing/guidelines/requirements/docs): Pipeline documentation should be provided within the `README.md`, `docs/usage.md` and `docs/output.md` files. Note that documentation does not need to be hosted on the nf-core website (unless the pipeline is meant to be contributed to nf-core).
- [ ] [Use nf-core git branches](https://nf-co.re/docs/contributing/guidelines/requirements/git_branches): Use `main` (instead of `master`), `dev` and `TEMPLATE`.

The following are additional requirements for [using nf-core components outside of nf-core][nf-core-outside-nf-core].

- [ ] [Acknowledging nf-core](https://nf-co.re/docs/contributing/tutorials/unofficial_pipelines#acknowledging-nf-core): Please add an acknowledgement statement to the pipeline `README.md` for nf-core.
- [ ] [Removing nf-core only branding](https://nf-co.re/docs/contributing/tutorials/unofficial_pipelines#removing-nf-core-only-branding): Please remove any nf-core only branding from your repository.

## 1.3. Requirements for integrating with IRIDA Next

Whether the pipeline you develop is intended to be contributed to nf-core or not, the following are additional requirements for integrating a pipeline with IRIDA Next. These may overlap with existing nf-core and Nextflow standards.

- [ ] [Input data using samplesheet and schema](#input): Please use a CSV samplesheet for referencing input data, where the structure of the CSV file is described in an `assets/schema_input.json` file. The JSON schema file will be used to render an interface in IRIDA Next for selecting and sending input data to be executed by a pipeline.
- [ ] [Parameters described in nextflow.config and nextflow_schema.json](#parameters): Please describe parameters for the pipeline in the `nextflow.config` and `nextflow_schema.json` file. The JSON schema file will be used by IRIDA Next to render an interface for selecting parameters to pass to the pipeline.
- [ ] [Output a JSON file describing data to load into IRIDA Next](#output): Please write an `output/iridanext.output.json.gz` file which describes the data to be loaded and stored within IRIDA Next (both files and metadata).
- [ ] [Describe resource requirements for each module](#resource-requirements): Please describe resource requirements using only the labels described in the above link.

## 1.4. Other requirements

The following are other requirements for developing pipelines.

- [ ] [Software security and vulnerabilities](#security): Please maintain good software security practices with pipeline code to be published. In particular, never store plain text passwords, API keys, ssh keys, or otherwise confidential information in software/pipelines which are distributed publicly. You may consider referring to the [GitHub Code Security](https://docs.github.com/en/code-security) documentation for securing your repository.

<a name="input"></a>

# 2. Input

Input for pipelines follows the [nfcore parameters][nfcore-parameters] specification. In particular, input data will be passed via a CSV file, where each row represents data that can be processed independently through the pipeline (commonly a sample). The input CSV file is passed using the `--input` parameter to a pipeline (e.g., `--input samples.csv`).

Within Nextflow, this `--input samples.csv` file is converted into a channel where each element within the channel corresponds to a single row of the input CSV file (e.g., each element in the channel contains the sample identifier and the input fastq files). This conversion from the input CSV file to a channel of tuples is performed using the [nf-validation fromSamplesheet][] function.

_Note: Commonly this `--input` CSV file is referred to as a samplesheet since it's often used to store data associated with a single sample, but it could be used to store other types of data as well._

## 2.1. Default samplesheet

The default samplesheet looks like:

| sample  | fastq_1      | fastq_2      |
| ------- | ------------ | ------------ |
| SampleA | file_1.fq.gz | file_2.fq.gz |

## 2.2. Types of input data

There are three types of columns within a samplesheet used for input: the **sample** identifier, **files**, and **metadata**.

### 2.2.1. Sample identifier

The sample identifier is used to match up data from the `--input samplesheet.csv` file to the individual samples this data is associated with. IRIDA Next expects the sample identifier to be stored within the `sample` column in the samplesheet. This identifier will be used to associate output files and metadata from pipelines with the respective sample (see the [4. Output](#4-output) section for details).

### 2.2.2. Input files

An input file consists of some data stored within a file external to the CSV file passed to `--input`. Commonly this would be sequence reads (and would be indicated with the `fastq_1` and `fastq_2` columns in the CSV file above). However, this could be any data intended to be delivered to pipeline steps via a channel (e.g., `bam` files of aligned reads, `fasta` files of assembled genomes, `csv` files of allelic profiles).

#### 2.2.2.1. Example sample input files

| sample  | fastq_1 **(an input file)** | fastq_2 **(another input file)** | assembly **(another input file)** |
| ------- | --------------------------- | -------------------------------- | --------------------------------- |
| SampleA | file_1.fq.gz                | file_2.fq.gz                     |                                   |
| SampleB |                             |                                  | assembly.fa.gz                    |

### 2.2.3. Metadata input types

A metadata input type is something that can be represented within a cell in a CSV file without reference to some external file (e.g., a Number or a String). In integration with IRIDA Next, these will be derived from contextual metadata of a sample (e.g., Organism).

#### 2.2.3.1. Example metadata input types

| sample  | organism **(metadata input)** | fastq_1      | fastq_2      |
| ------- | ----------------------------- | ------------ | ------------ |
| SampleA | Salmonella enterica           | file_1.fq.gz | file_2.fq.gz |

## 2.3. Defining the samplesheet structure for a pipeline

The structure of an input samplesheet for a pipeline (i.e., column names and data types) is defined within a JSON Schema file found within the `assets/schema_input.json` file of a pipeline. This JSON Schema file is used by the [nf-validation][] plugin in Nextflow. This plugin defines the keywords used to add new columns to the input samplesheet, documented in the [nf-validation samplesheet schema specification][nf-validation samplesheet]. This JSON schema file also accepts the definition of rules for validating the data passed within the samplesheet (e.g., if a column requires only numeric data, this can be defined within the JSON schema).

By default, the samplesheet schema assumes you have three input columns: `sample`, `fastq_1`, and `fastq_2`. Single-end data is handled by leaving `fastq_2` blank.

For custom types of data for a pipeline, please modify this JSON schema by modifying the `assets/schema_input.json` file. Also, make sure your pipeline is configured to use [nf-validation fromSamplesheet][nf-validation fromsamplesheet] to create a channel of input data for your pipeline from an input CSV file that follows this JSON schema.

### 2.3.1. Defining sample identifiers in IRIDA Next

Sample identifiers from IRIDA Next are placed into an input samplesheet.csv for a Nextflow pipeline in the `sample` column. That is, if the expected samplesheet structure is as below:

**samplesheet.csv**
| sample |
|---------|
| SampleA |
| SampleB |

Then the following should be defined in the `assets/schema_input.json`:

**assets/schema_input.json**

```json
{
  // ...
  "items": {
    "type": "object",
    "properties": {
      "sample": {
        "type": "string",
        "pattern": "^\\S+$",
        "meta": ["id"]
      }
    }
  }
}
```

Here, `"meta": ["id"]` is used internally by Nextflow and the nf-validation plugin to map the identifier in the `sample` column in the samplesheet.csv to a `meta.id` value, which is expected to be passed alongside any input data in a channel for Nextflow/nf-core processes. See the [nf-validation Sample sheet keys](https://nextflow-io.github.io/nf-validation/latest/nextflow_schema/sample_sheet_schema_specification/#sample-sheet-keys) documentation for more details.

### 2.3.2. Defining selection of input files in IRIDA Next

In order to configure a pipeline to select input files from IRIDA Next, please make sure the samplesheet `assets/schema_input.json` for the column contains the following keywords:

- _`"format": "file-type"`_: This is used by IRIDA Next to indicate that a particular column/field in the samplesheet should be a file associated with a sample. This is also by the nf-validation plugin in Nextflow to validate the respective column as containing a file path and check for existence of this file. See the [nf-validation schema specification](https://nextflow-io.github.io/nf-validation/latest/nextflow_schema/nextflow_schema_specification/#format) for more details.
- _`"pattern": ".*.f(ast)?q(.gz)?$"`_: This is used by IRIDA Next to constrain what sorts of files attached to a sample can be used as input to this column (e.g., reads in fastq format, assemblies in fasta format, etc). The value here is a regular expression for a file path. On the Nextflow nf-validation side, this is used to also validate the input paths in the samplesheet are as expected (i.e., they match the defined regular expression).
- _`fastq_1` and `fastq_2` for **paired/single-end input fastqs**_: The samplesheet column names `fastq_1` and `fastq_2` should be used to indicate input paired/single-end fastq files. Using these column names will mean IRIDA Next will make sure the pairs of reads are properly paired (i.e., if there's multiple read sets attached to a sample it will guarantee that only pairs of reads associated with each other will be allowed as input to the pipeline).
- _Other input files (other than paired/single-end fastqs) can use any other column name._

#### 2.3.2.1. Example samplesheet and JSON schema for an assembly

As an example, if a pipeline is intended to take as input an assembled genome (in fasta format) for each sample, the following would be how the samplesheet could be defined:

**samplesheet.csv**
| sample | assembly |
|---------|-------------|
| SAMPLE1 | s1.fasta.gz |
| SAMPLE2 | s2.fasta.gz |

The following would be an example structure found within the `assets/schema_input.json` file:

**assets/schema_input.json**

```json
{
  // ...
  "items": {
    "type": "object",
    "properties": {
      "sample": {
        "type": "string",
        "pattern": "^\\S+$",
        "meta": ["id"]
      },
      "assembly": {
        "type": "string",
        "format": "file-path",
        "pattern": "^\\S+\\.f(ast)?a(\\.gz)?$"
      }
    }
  }
}
```

### 2.3.3. Defining selection of input metadata in IRIDA Next

In order to configure a pipeline to select input metadata from IRIDA Next, please make sure the samplesheet `assets/schema_input.json` for the column contains **does not** contain `"format": "file-path"`. That is:

- _**Does not** contain `"format": "file-path"`_: This is the key part of defining selection of metadata for IRIDA Next (the assumption being anything that is not a file is metadata).
- _**Does not** use column names `sample`, `fastq_1`, or `fastq_2`_: These are reserved column names in a samplesheet for IRIDA Next.
- _The column name in samplesheet should match metadata field_: IRIDA Next will attempt to automatically select metadata for a pipeline based on the column name in the input samplesheet for a pipeline. That is, if the samplesheet names a column `organism`, then IRIDA Next will attempt to auto-populate the `organism` metadata field from the sample data when running the pipline. Users have the option in IRIDA Next to override the selected metadata key when running a pipeline (e.g., if the metadata field in IRIDA Next to use is named _species_ instead).

#### 2.3.3.1. Example samplesheet and JSON schema for a metadata value

As an example, if a pipeline is intended to take as input a metadata value, the following would be how the samplesheet could be defined:

**samplesheet.csv**
| sample | organism |
|---------|-------------|
| SAMPLE1 | Salmonella |
| SAMPLE2 | Salmonella |

The following would be an example structure found within the `assets/schema_input.json` file:

**assets/schema_input.json**

```json
{
  // ...
  "items": {
    "type": "object",
    "properties": {
      "sample": {
        "type": "string",
        "pattern": "^\\S+$",
        "meta": ["id"]
      },
      "organism": {
        "type": "string"
      }
    }
  }
}
```

IRIDA Next will attempt to autopopulate the `organism` column in the samplesheet from the `oranism` metadata field for samples, but provides a mechanism to override the selected metadata field for samples.

<a name="parameters"></a>

# 3. Parameters

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

<a name="output"></a>

# 4. Output

## 4.1. Files

Output files will be placed into a directory specified by the `--outdir` parameter. For examples given below we assume `--outdir output` was used.

## 4.2. IRIDA Next JSON

An `output/iridanext.output.json.gz` file will be written, which specifies the files and metadata to store into IRIDA Next.

### 4.2.1. Minimal example

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

### 4.2.2. Complete example

A complete example of this file with more data filled in is as below.

**output/iridanext.output.json.gz**:

```json
{
  "files": {
    "global": [{ "path": "summary.txt" }],
    "samples": {
      "SampleA": [
        { "path": "assembly/assembly.fa.gz" },
        { "path": "assembly/annotation.gbk.gz" }
      ]
    }
  },
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
}
```

### 4.2.3. Output files

The `files` section lists the files to store within IRIDA Next (files not listed here will not be stored). This consists of two different sections:

#### 4.2.3.1. global

The `global` section defines a list of files associated with the pipeline globally. Each file entry is a JSON object with a single key `path` defining the path to the file (relative to the `output` directory). More keys may be added in the future.

```json
"global": [
    {"path": "summary.txt"}
]
```

#### 4.2.3.2. samples

The `samples` section defines a list of files to be associated with each sample individual (e.g., genome assemblies). Each sample identifier should correspond to the same identifer specified in the `--input` samplesheet. Each file entry is a JSON object with a single key `path` defining the path to the file (relative to the `output` directory).

```json
"samples": {
    "SampleA": [
        {"path": "assembly/assembly.fa.gz"}
    ]
}
```

### 4.2.4. Metadata

The `metadata` section contains structured data to load within the IRIDA Next database. Currently there is only one type of metadata, `samples`.

The `samples` metadata object consists of keys corresponding to sample identifiers used in the original input samplesheet file. The values consist of metadata values to store in IRIDA Next.

As of right now, only simple key-value pairs are supported as metadata. Support for more complicated JSON structures may be added in the future.

```json
"metadata": {
    "samples": {
        "SampleA": {
            "organism": "Salmonella enterica",
            "amr.1.gene": "x",
            "amr.1.start": 1234,
            "amr.1.end": 5678,
        }
    }
}
```

### 4.2.5. Creating the IRIDA Next JSON integration file

While the `iridanext.output.json.gz` integration file can be generated by custom code in a pipeline, in most use cases the expectation is to make use of a Nextflow plugin for this purpose: [nf-iridanext][].

The **nf-iridanext** Nextflow plugin can be loaded by a pipeline by adding it to the `plugins` section within the `nextflow.config`.

```conf
plugins {
    id 'nf-iridanext@0.2.0'
}
```

This adds support for a custom `iridanext` scoped block as part of the Nextflow configuration that controls the generation of an IRIDA Next JSON integration file.

The `iridanext` configuration values can be added anywhere within the Nextflow configuration files. One way to organize the configuration is to create a separate `conf/iridanext.config` file in the pipeline with the IRIDA Next configuration, and include this file in the `nextflow.config`.

**nextflow.config**

```conf
includeConfig 'conf/iridanext.config'
```

**conf/iridanext.config**

```conf
iridanext {
    enabled = true
    output {
        path = "${params.outdir}/iridanext.output.json.gz"
    }
}
```

When setup this way, the pipeline will generate an `iridanext.output.json.gz` file within the passed `--outdir` parameter with the following minimal contents:

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

Please see the [nf-iridanext documentation][nf-iridanext] for more details on integrating output files and metadata produced from the pipeline into this IRIDA Next integration file.

# 5. Modules

[Modules in Nextflow][nextflow-modules] are scripts that can contain functions, processes, or workflows.

## 5.1. nf-core modules

Nf-core contains a number of modules for processes to execute different bioinformatics tools ([nf-core modules][]). Where possible, please use nf-core modules within your pipeline. These can be installed via running the following command from the [nf-core tools][nf-core-modules-install].

```bash
nf-core modules install [NAME]
```

## 5.2. Local modules

If it is not possible to use existing nf-core modules, you can create your own modules local to your pipeline in the `modules/local/` directory. Please see the [nf-core contributing modules][nf-core-contributing-modules] documentation for expectations on how modules should behave. Local modules won't need to follow all of these guidelines (such as uploading to the list of nf-core approved modules), but the behaviour of inputs, parameters, and outputs of a process should be followed as closely as possible.

### 5.2.1. Module software requirements

Each module should define its software requirements as a docker/singularity container. This requires using the `container` keyword in the process. For example:

```
container "${ workflow.containerEngine == 'singularity' && !task.ext.singularity_pull_docker_container ?
    'https://depot.galaxyproject.org/singularity/fastqc:0.11.9--0' :
    'biocontainers/fastqc:0.11.9--0' }"
```

For IRIDA Next Nextflow pipelines running in Azure and using Docker, the default container registry should be set to the publicly available `quay.io` registry in the `nextflow.config` file using the `docker.registry` parameter.

For more information, see the [Nextflow containers][] documentation and the [nf-core modules software requirements][] guide.

### 5.2.2 Configuring Module Software with Private Container Registries

In some cases, you may want to use a private container registry instead of, or in addition to, a public container registry to define software requirements for Nextflow pipeline modules. For this purpose, we introduce the `--override_default_container_registry` parameter. This boolean parameter allows the Nextflow pipeline to override a default container registry with an alternative registry. By default, this parameter is set to `false`, and the default container registry, specified in the `nextflow.config` file, is used.

To specify an alternative registry for a module, the `container` directive should incorporate a conditional check using the `params.override_default_container_registry` within a nested ternary operator in its closure.

Below is an example of a `container` directive specifying the software requirements for a module in an IRIDA Next Nextflow pipeline:

```
container "${ workflow.containerEngine == 'singularity' && !task.ext.singularity_pull_docker_container ?
    'https://depot.galaxyproject.org/singularity/fastqc:0.11.9--0' :
    (params.override_default_container_registry ? 'docker.io/biocontainers/fastqc:v0.11.9_cv8' : 'biocontainers/fastqc:0.11.9--0') }"
```

In this example, when the `--override_default_container_registry` parameter is set to `true`, the default Docker registry (`quay.io`) is overridden, and the pipeline uses the DockerHub container registry (`docker.io`) instead.

When running on IRIDA Next, the Azure configuration file will automatically set the `--override_default_container_registry` parameter to `true` to ensure that the alternative registry can be used for any processes with the `container` directive conditional check.

#### 5.2.2.1 Additional Configurations for Supporting the `--override_default_container_registry` Parameter

To support the dynamic selection of container registries within IRIDA Next Nextflow pipeline modules when using the `--override_default_container_registry` parameter, the following additions need to be added to the `nextflow.config` and `nextflow_schema.json` files:

**nextflow.config**

Add the `override_default_container_registry` parameter to the `nextflow.config` file:

```
params {

    // Override default container registry
    override_default_container_registry = false

}
```

**nextflow_schema.json**

Add the new parameter to the definition input/output properties in the `nextflow_schema.json` file:

```
{
  "definitions": {
    input_output_options": {
      "properties" : {
        "override_default_container_registry": {
            "type": "boolean",
            "description": "Determines whether to override the default Docker registry (quay.io) with an alternate container registry (e.g. docker.io). Defaults to false.",
            "fa_icon": "fas fa-question-circle",
            "default": false
        }
      }
    }
  }
}
```

<a name="resource-requirements"></a>

# 6. Resource requirements

To define computational resource requirements for each process, we will follow the [nf-core resource][nf-core-module-resource] standards as much as possible, where resources are adjusted by a `label` in each `process` of a pipeline. The pipeline developer will be responsible for setting an appropriate `label` in each process of the NextFlow pipeline to appropriatly define required resources. In addition, the developer is responsible for tuning any resources defined in the `config/base.config` file, as described in the [nf-core tuning workflow resources][] documentation.

## 6.1. Process resource label

The pipeline developer will add a `label` to each `process` of a NextFlow pipeline to adjust resources. For example:

```
process name {
    label 'process_single'

    // ...
}
```

### 6.1.1. Accepted resource labels

The following labels will be accepted:

- `proccess_single`
- `process_low`
- `process_medium`
- `process_high`
- `process_very_high`: This label is an addition over those provided by nf-core to be used for situations where a process needs a lot of resources beyond `process_high`.

## 6.2. Tuning resource limits with parameters

Nf-core provides the capability to adjust the maximum resources given to processes in a pipeline using parameters: `--max_cpus`, `--max_memory`, and `--max_time`. Pipeline developers will be responsible for making sure thse parameters are available in the `nextflow.config` file. See the [nf-core max resources][] documentation for more details.

# 7. Testing

Nextflow pipelines should include test suites, linting, and execution of these tests automatically on merging of new code (i.e., continuous integration). These files should be included automatically if the pipeline is generated using the [nf-core create][nf-core-create] command. A description of these different tests are included in the [nf-core contributing][nf-core-contributing-github-actions] documentation.

## 7.1. Nextflow test profile

In particular, a `test` profile should be setup in Nextflow so that the pipeline can be executed with `nextflow [NAME] -profile test,docker`.

Parameters for the `test` profile should be specified in the `conf/test.config` file. This can reference data stored elsewhere (e.g., on GitHub). For example, see the `test.config` for the IRIDA Next sample pipeline <https://github.com/phac-nml/iridanext-example-nf/blob/main/conf/test.config>.

## 7.2. Unit/integration tests

It is also expected to include with each pipeline a set of unit/integration tests. These can be implemented by using [nf-test][].

Documentation is provided on the [nf-test website][nf-test], but the quick way to get started is to first install `nf-test` (e.g., via conda):

```bash
conda create --name nextflow nextflow nf-test
```

Then, run the following to create the necessary files for `nf-test` tests:

```bash
nf-test init
```

The nf-core templates for pipelines should include a GitHub actions task for running `nf-test` on pull-requests. Please refer to the [nf-test documentation][nf-test] for more details.

# 8. Executing pipelines

## 8.1. Standalone execution

Pipelines should be configured so that they can be executed by:

```bash
nextflow run [NAME] --input inputsheet.csv --outdir output [OTHER PARAMETERS]
```

Where `inputsheet.csv` is the CSV file containing samples (or other input data) and `output` is the directory containing output data. Other parameters can be included, though reasonable defaults should be set so the number of required parameters is as minimal as possible.

## 8.2. Execution via GA4GE WES

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

## 8.3. Running pipelines in a local instance of IRIDA Next

For detailed instructions and configuration options, please refer to the the guide for setting up a local instance of IRIDA Next:

**[IRIDANext_LocalGuide.md](docs/IRIDANext_LocalGuide.md)**

The guide includes step-by-step instructions for installing required dependencies, setting up the development environment, and configuring the necessary services.

- **Summary of Installation Steps**
  - Install asdf: A tool version manager for managing dependencies.
  - Install Required Plugins: Node.js, pnpm, Ruby, and Postgres using asdf.
  - Setup and Start Postgres Server: Create a new role and configure the development database.
  - Configure Environment: Set up credentials and active storage for IRIDA Next.
  - Setup Sapporo: Install and configure Sapporo for managing GA4GH WES instances.
  - Register Pipelines: Add and register Nextflow pipelines in IRIDA Next.
  - Run Local Instance: Start the web application and access it via a web browser.

# 9. Resources

## 9.1. Pipeline development tutorial

A tutorial on developing a pipeline is available at [IRIDA Next nf-core pipeline tutorial][pipeline-tutorial].

## 9.2. List of pipelines

An example pipeline that conforms to these standards is available at <https://github.com/phac-nml/iridanext-example-nf>.

Other pipelines are listed at <https://github.com/phac-nml/nf-pipelines>.

## 9.3. Other resources

- [PHA4GE proposed pipeline standards][pha4ge-pipeline-standards]: Proposed pipeline standards from the PHA4GE Bioinformatics Pipelines & Visualization Working Group
- [Government of Canada: Guide for Publishing Open Source Code][gov-canada-open-source-software]
- [nfcore pipeline schema][nfcore-pipeline-schema]
- [nfcore parameters][nfcore-parameters]
- [GA4GH Workflow Execution Service][ga4gh-wes]

# 10. Future pipeline integration ideas

Additional ideas for integrating pipelines in the future can be found in the [IRIDA Next pipeline integration ideas][iridanext-ideas] document.

<a name="security"></a>

# 11. Security practices

Please maintain good software security practices with pipeline code to be published. In particular, never store plain text passwords, API keys, ssh keys, or otherwise confidential information in software/pipelines which are distributed publicly.

## 11.1. Automated repository scanning (recommendations)

There exist automated tools to aid in maintining secure code and identifying potential issues. The [GitHub Securing your repository][github-secure-repository] documentation provides detailed information on how to secure your GitHub repository, which includes automated scanning. In particular, some recommendations include:

- [Enable secret scanning][github-secret-scanning]: This will scan code and issues for patterns potentially matching secrets (e.g., API keys) that should not be made public and provide alerts for anything that was found.
- [Enable code scanning][github-codeql]: GitHub provides CodeQL for scanning code for potential vulnerabilities or errors in your code and providing recommended fixes. While this does not support the Nextflow language, other programming languages (e.g., Python) are supported.
- [Enable dependabot][github-dependabot]: Dependabot scans for and provides alerts on insecure dependencies of your software. While [Nextflow is not directly supported by github][github-core-languages], this might be a service of use for other repositories or other code and dependencies managed within a pipeline.

Enabling these services can be done directly on a GitHub repository in the **Settings > Code security and analysis** section.

![security-settings-github.png][]

Once enabled, security alerts appear in the **Security** tab on GitHub.

![security-alerts.png][]

For more security recommendations, please see the [PHA4GE pipeline guidelines][pha4ge-pipeline-security].

# 12. Publishing guidelines

Our intention is to follow, as much as possible, the standards and practices set out by nf-core. However, we leave it as optional to actually publish pipelines/modules/subworkflows with the official nf-core repositories. We would encourage this where it makes sense in order to support re-use and giving back to the community (please refer to the [nf-core publishing requirements][] for the guidelines in this case). However, it is perfectly acceptible to publish pipelines/modules/subworkflows in separate Git repositories outside of nf-core. Please see the [if the guidelines don't fit][nf-core-external-development] and [using nf-core components outside of nf-core][nf-core-outside-nf-core] sections of the nf-core documentation for more information on this scenario and other locations to list your pipeline.

# 13. Legal

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
[nf-core-pipelines]: https://nf-co.re/pipelines
[nf-core-guidelines]: https://nf-co.re/docs/contributing/guidelines
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
[nf-validation samplesheet]: https://nextflow-io.github.io/nf-validation/latest/nextflow_schema/sample_sheet_schema_specification/
[nf-validation fromSamplesheet]: https://nextflow-io.github.io/nf-validation/latest/samplesheets/fromSamplesheet/
[nf-validation]: https://nextflow-io.github.io/nf-validation/latest/
[iridanext-samplesheet]: ideas.md#samplesheets
[iridanext-ideas]: ideas.md
[pha4ge-pipeline-standards]: https://github.com/pha4ge/pipeline-resources/blob/main/docs/pipeline-standards.md
[pipeline-tutorial]: https://github.com/apetkau/nf-core-assemblyexample
[nf-core-add-pipeline]: https://nf-co.re/docs/contributing/adding_pipelines
[nf-core-requirements]: https://nf-co.re/docs/contributing/guidelines#requirements
[gov-canada-open-source-software]: https://www.canada.ca/en/government/system/digital-government/digital-government-innovations/open-source-software/guide-for-publishing-open-source-code.html
[pha4ge-pipeline-security]: https://github.com/pha4ge/pipeline-resources/blob/main/docs/pipeline-standards.md#software-security-and-vulnerabilities
[github-secret-scanning]: https://docs.github.com/en/code-security/getting-started/securing-your-repository#configuring-secret-scanning
[github-codeql]: https://docs.github.com/en/code-security/getting-started/securing-your-repository#configuring-code-scanning
[github-dependabot]: https://docs.github.com/en/code-security/getting-started/securing-your-repository#managing-dependabot-alerts
[github-core-languages]: https://docs.github.com/en/get-started/learning-about-github/github-language-support#core-languages-supported-by-github-features
[github-secure-repository]: https://docs.github.com/en/code-security/getting-started/securing-your-repository
[security-settings-github.png]: images/security-settings-github.png
[security-alerts.png]: images/security-alerts.png
[nf-iridanext]: https://github.com/phac-nml/nf-iridanext
[nf-test]: https://www.nf-test.com/
