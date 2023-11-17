# IRIDA Next Nextflow pipeline specification

*This document is in-progress and should be considered as a **DRAFT** that is subject to change at any time.*

This document describes the specification for developing [IRIDA Next][irida-next] Nextflow pipelines.

- [1. Starting development](#1-starting-development)
  * [1.1. Developing a pipeline to contribute to nf-core](#11-developing-a-pipeline-to-contribute-to-nf-core)
  * [1.2. Developing a pipeline which won't be contributed to nf-core](#12-developing-a-pipeline-which-wont-be-contributed-to-nf-core)
    + [1.2.1. Requirements from nf-core](#121-requirements-from-nf-core)
  * [1.3. Requirements for integrating with IRIDA Next](#13-requirements-for-integrating-with-irida-next)
  * [1.4. Other requirements][#14-other-requirements]
- [2. Input](#2-input)
  * [2.1. Default samplesheet](#21-default-samplesheet)
  * [2.2. Types of input data](#22-types-of-input-data)
    + [2.2.1. Input files](#221-input-files)
      - [2.2.1.1. Example sample input files](#2211-example-sample-input-files)
      - [2.2.1.2. Example allelic profile input files](#2212-example-allelic-profile-input-files)
    + [2.2.2. Simple input types](#222-simple-input-types)
      - [2.2.2.1. Example simple input types](#2221-example-simple-input-types)
  * [2.3. Modify samplesheet](#23-modify-samplesheet)
    + [2.3.1. Update samplesheet schema specification](#231-update-samplesheet-schema-specification)
      - [2.3.1.1. Select column names from IRIDA Next keywords](#2311-select-column-names-from-irida-next-keywords)
- [3. Parameters](#3-parameters)
- [4. Output](#4-output)
  * [4.1. Files](#41-files)
  * [4.2. IRIDA Next JSON](#42-irida-next-json)
    + [4.2.1. Minimal example](#421-minimal-example)
    + [4.2.2. Complete example](#422-complete-example)
    + [4.2.3. Output files](#423-output-files)
      - [4.2.3.1. global](#4231-global)
      - [4.2.3.2. samples](#4232-samples)
    + [4.2.4. Metadata](#424-metadata)
      - [4.2.4.1. Simplified metadata JSON](#4241-simplified-metadata-json)
- [5. Modules](#5-modules)
  * [5.1. nf-core modules](#51-nf-core-modules)
  * [5.2. Local modules](#52-local-modules)
    + [5.2.1. Module software requirements](#521-module-software-requirements)
- [6. Resource requirements](#6-resource-requirements)
  * [6.1. Process resource label](#61-process-resource-label)
    + [6.1.1. Accepted resource labels](#611-accepted-resource-labels)
  * [6.2. Tuning resource limits with parameters](#62-tuning-resource-limits-with-parameters)
- [7. Testing](#7-testing)
  * [7.1. Nextflow test profile](#71-nextflow-test-profile)
- [8. Executing pipelines](#8-executing-pipelines)
  * [8.1. Standalone execution](#81-standalone-execution)
  * [8.2. Execution via GA4GE WES](#82-execution-via-ga4ge-wes)
- [9. Resources](#9-resources)
  * [9.1. Pipeline development tutorial](#91-pipeline-development-tutorial)
  * [9.2. List of pipelines](#92-list-of-pipelines)
  * [9.3. Other resources](#93-other-resources)
- [10. Future pipeline integration ideas](#10-future-pipeline-integration-ideas)
- [11. Publishing guidelines](#11-publishing-guidelines)
- [12. Legal](#12-legal)

# 1. Starting development

IRIDA Next pipelines attempt to follow as much as possible the [nf-core][] guidelines for development pipelines. As a preliminatry first step prior to developing pipelines in IRIDA Next, **please read through the following documentation:**

* [Nf-core guidelines][nf-core-guidelines]
* [Nf-core adding a new pipeline][nf-core-add-pipeline]

While we do follow nf-core as closely as possible, there are a number of differences or additions we make for integrating pipelines into IRIDA Next. In particular, while pipelines can be contributed to the [nf-core index of pipelines][nf-core-pipelines], we do not require them to be as we may have different requirements for our own pipelines. Hence, some of the nf-core requirements may not apply if you do not intend to contribute the pipeline to nf-core.

## 1.1. Developing a pipeline to contribute to nf-core

If you intend to develop a pipeline to contribute to nf-core, please follow all the [nf-core requirements][nf-core-requirements] in addition to the requirements listed in this guide.

## 1.2. Developing a pipeline which won't be contributed to nf-core

If, instead, the pipeline won't be contributed to nf-core, then the only following set of [nf-core requirements][nf-core-requirements] would need to be followed in addition to the [if the guidelines don't fit][nf-core-external-development] section of the nf-core guidelines, [using nf-core components outside of nf-core][nf-core-outside-nf-core] section, and the below IRIDA Next requirements sections.

### 1.2.1. Requirements from nf-core

* [ ] Nextflow: Workflows must be built using Nextflow.
* [ ] [Workflow name](https://nf-co.re/docs/contributing/guidelines/requirements/workflow_name): Names should be lower case and without punctuation.
* [ ] [Use the template](https://nf-co.re/docs/contributing/guidelines/requirements/use_the_template): All nf-core pipelines must be built using the nf-core template.
* [ ] [Software license](https://nf-co.re/docs/contributing/guidelines/requirements/mit_license): Pipelines must open source, released with the MIT license.
* [ ] [Docker support](https://nf-co.re/docs/contributing/guidelines/requirements/docker): Software must be bundled using Docker and versioned.
* [ ] [Continuous integration testing](https://nf-co.re/docs/contributing/guidelines/requirements/ci_testing): Pipelines must run CI tests.
* [ ] [Semantic versioning](https://nf-co.re/docs/contributing/guidelines/requirements/semantic_versioning): Pipelines must use stable release tags.
* [ ] [Standardised parameters](https://nf-co.re/docs/contributing/guidelines/requirements/parameters): Strive to have standardised usage.
* [ ] [Single command](https://nf-co.re/docs/contributing/guidelines/requirements/single_command): Pipelines should run in a single command.
* [ ] [Keywords](https://nf-co.re/docs/contributing/guidelines/requirements/keywords): Excellent documentation and GitHub repository keywords.
* [ ] [Pass lint tests](https://nf-co.re/docs/contributing/guidelines/requirements/linting): The pipeline must not have any failures in the nf-core lint tests.
    * *Note: It is possible to ignore some of the default nf-core linting with a [lint configuration file](https://nf-co.re/tools#linting-config).*
* [ ] [Credits and Acknowledgements](https://nf-co.re/docs/contributing/guidelines/requirements/acknowledgements): Pipelines must properly acknowledge prior work.
* [ ] [Minimum inputs](https://nf-co.re/docs/contributing/guidelines/requirements/minimum_inputs): Pipelines should be able to run with as little input as possible.

 The following are modified nf-core requirements:
 * [ ] [Bundled documentation](https://nf-co.re/docs/contributing/guidelines/requirements/docs): Pipeline documentation should be provided within the `README.md`, `docs/usage.md` and `docs/output.md` files. Note that documentation does not need to be hosted on the nf-core website (unless the pipeline is meant to be contributed to nf-core).
 * [ ] [Use nf-core git branches](https://nf-co.re/docs/contributing/guidelines/requirements/git_branches): Use `main` (instead of `master`), `dev` and `TEMPLATE`.
     
 The following are additional requirements for [using nf-core components outside of nf-core][nf-core-outside-nf-core].
 * [ ] [Acknowledging nf-core](https://nf-co.re/docs/contributing/tutorials/unofficial_pipelines#acknowledging-nf-core): Please add an acknowledgement statement to the pipeline `README.md` for nf-core.
 * [ ] [Removing nf-core only branding](https://nf-co.re/docs/contributing/tutorials/unofficial_pipelines#removing-nf-core-only-branding): Please remove any nf-core only branding from your repository.

## 1.3. Requirements for integrating with IRIDA Next

Whether the pipeline you develop is intended to be contributed to nf-core or not, the following are additional requirements for integrating a pipeline with IRIDA Next. These may overlap with existing nf-core and Nextflow standards.

* [ ] [Input data using samplesheet and schema](#input): Please use a CSV samplesheet for referencing input data, where the structure of the CSV file is described in an `assets/schema_input.json` file. The JSON schema file will be used to render an interface in IRIDA Next for selecting and sending input data to be executed by a pipeline.
* [ ] [Parameters described in nextflow.config and nextflow_schema.json](#parameters): Please describe parameters for the pipeline in the `nextflow.config` and `nextflow_schema.json` file. The JSON schema file will be used by IRIDA Next to render an interface for selecting parameters to pass to the pipeline.
* [ ] [Output a JSON file describing data to load into IRIDA Next](#output): Please write an `output/iridanext.output.json.gz` file which describes the data to be loaded and stored within IRIDA Next (both files and metadata).
* [ ] [Describe resource requirements for each module](#resource-requirements): Please describe resource requirements using only the labels described in the above link.

## 1.4. Other requirements

The following are other requirements for developing pipelines.

* [ ] [Software security and vulnerabilities](https://github.com/pha4ge/pipeline-resources/blob/main/docs/pipeline-standards.md#software-security-and-vulnerabilities): Please maintain good software security practices with pipeline code to be published. In particular, never storing plain text passwords, API keys, ssh keys, or otherwise confidential information in software/pipelines which are distributed publicly. See the [PHA4GE pipeline guidelines][pha4ge-pipeline-security] for additional recommendations details.

<a name="input"></a>
# 2. Input

Input for pipelines follows the [nfcore parameters][nfcore-parameters] specification. In particular, input data will be passed via a CSV file, where each row represents data that can be processed independently through the pipeline (commonly a sample). The input CSV file is passed using the `--input` parameter to a pipeline (e.g., `--input samples.csv`).

Within Nextflow, this `--input samples.csv` file is converted into a channel where each element within the channel corresponds to a single row of the input CSV file (e.g., each element in the channel contains the sample identifier and the input fastq files). This conversion from the input CSV file to a channel of tuples is performed using the [nf-validation fromSamplesheet][] function.

*Note: Commonly this `--input` CSV file is referred to as a samplesheet since it's often used to store data associated with a single sample, but it could be used to store other types of data as well.*

## 2.1. Default samplesheet

The default samplesheet looks like:

| sample  | fastq_1      | fastq_2      |
|---------|--------------|--------------|
| SampleA | file_1.fq.gz | file_2.fq.gz |

## 2.2. Types of input data

There are two types of input data: **files** and **simple**.

### 2.2.1. Input files

An input file consists of some data stored within a file external to the CSV file passed to `--input`. Commonly this would be sequence reads (and would be indicated with the `fastq_1` and `fastq_2` columns in the CSV file above). However, this could be any data intended to be delivered to pipeline steps via a channel (e.g., `bam` files of aligned reads, `fasta` files of assembled genomes, `csv` files of allelic profiles).

#### 2.2.1.1. Example sample input files

| sample  | fastq_1 **(an input file)** | fastq_2 **(another input file)** | assembly **(another input file)** |
|---------|--------------|--------------|----|
| SampleA | file_1.fq.gz | file_2.fq.gz | |
| SampleB | | | assembly.fa.gz |

#### 2.2.1.2. Example allelic profile input files

| profile_id | scheme    | alleles **(input file)** |
|------------|-----------|--------------------------|
| ProfilesA  | senterica | alleles.tsv.gz           |

### 2.2.2. Simple input types

A simple input type is something that can be represented within a cell in a CSV file without reference to some external file (e.g., a Number or a String). In integration with IRIDA Next, these will often be derived from contextual metadata of a sample (e.g., Organism) or will be the sample identifier.

#### 2.2.2.1. Example simple input types

| sample **(simple input)**  | organism **(simple input)** | fastq_1      | fastq_2      |
|----------------------------|-----------------------------|--------------|--------------|
| SampleA                    | Salmonella enterica         | file_1.fq.gz | file_2.fq.gz |

## 2.3. Modify samplesheet

In order to modify the samplesheet structure there are two main tasks to complete.

### 2.3.1. Update samplesheet schema specification

The [nf-validation samplesheet schema specification][nf-validation samplesheet] defines the structure of the input samplesheet for a pipeline. This consists of a JSON schema file and can be modified to add different columns to the samplesheet for different types of input. This file also accepts the definition of rules for validating the data passed within the samplesheet (e.g., if a column requires only numeric data, this can be defined within the JSON schema).

By default, the samplesheet schema assumes you have three input columns: `sample`, `fastq_1`, and `fastq_2`. Single-end data is handled by leaving `fastq_2` blank.

For custom types of data for a pipeline, please modify this JSON schema by modifying the `assets/schema_input.json` file. Also, make sure your pipeline is configured to use [nf-validation fromSamplesheet][nf-validation fromsamplesheet] to create a channel of input data for your pipeline from an input CSV file that follows this JSON schema.

#### 2.3.1.1. Select column names from IRIDA Next keywords

In order to select data from IRIDA Next, please use one of the following keywords as column names in the `--input` CSV file.

* **sample**: The IRIDA Next sample identifier.
* **fastq_1**: The first of paired-end Illumina fastq files (or a single-end Illumina fastq file).
* **fastq_2**: The second of paired-end Illumina fastq files (leave empty if using single-end data).
* **assembly_fasta**: The assembled genome (in FASTA format).

For additional ideas of a more advanced method of selecting data from IRIDA Next, please see [IRIDA Next samplesheet ideas documentation][iridanext-samplesheet].

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

#### 4.2.4.1. Simplified metadata JSON

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

# 5. Modules

[Modules in Nextflow][nextflow-modules] are scripts that can contain functions, processes, or workflows.

## 5.1. nf-core modules

Nf-core contains a number of modules for processes to execute different bioinformatics tools ([nf-core modules][]). Where possible, please use nf-core modules within your pipeline. These can be installed vi running the following command from the [nf-core tools][nf-core-modules-install].

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

For more information, see the [Nextflow containers][] documentation and the [nf-core modules software requirements][] guide.

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

* `proccess_single`
* `process_low`
* `process_medium`
* `process_high`
* `process_very_high`: This label is an addition over those provided by nf-core to be used for situations where a process needs a lot of resources beyond `process_high`.

## 6.2. Tuning resource limits with parameters

Nf-core provides the capability to adjust the maximum resources given to processes in a pipeline using parameters: `--max_cpus`, `--max_memory`, and `--max_time`. Pipeline developers will be responsible for making sure thse parameters are available in the `nextflow.config` file. See the [nf-core max resources][] documentation for more details.

# 7. Testing

Nextflow pipelines should include test suites, linting, and execution of these tests automatically on merging of new code (i.e., continuous integration). These files should be included automatically if the pipeline is generated using the [nf-core create][nf-core-create] command. A description of these different tests are included in the [nf-core contributing][nf-core-contributing-github-actions] documentation.

## 7.1. Nextflow test profile

In particular, a `test` profile should be setup in Nextflow so that the pipeline can be executed with `nextflow [NAME] -profile test,docker`.

Parameters for the `test` profile should be specified in the `conf/test.config` file. This can reference data stored elsewhere (e.g., on GitHub). For example, see the `test.config` for the IRIDA Next sample pipeline <https://github.com/phac-nml/iridanext-example-nf/blob/main/conf/test.config>.

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

# 9. Resources

## 9.1. Pipeline development tutorial

A tutorial on developing a pipeline is available at [IRIDA Next nf-core pipeline tutorial][pipeline-tutorial].

## 9.2. List of pipelines

An example pipeline that conforms to these standards is available at <https://github.com/phac-nml/iridanext-example-nf>.

Other pipelines are listed at <https://github.com/phac-nml/nf-pipelines>.

## 9.3. Other resources

* [PHA4GE proposed pipeline standards][pha4ge-pipeline-standards]: Proposed pipeline standards from the PHA4GE Bioinformatics Pipelines & Visualization Working Group
* [Government of Canada: Guide for Publishing Open Source Code][gov-canada-open-source-software]
* [nfcore pipeline schema][nfcore-pipeline-schema]
* [nfcore parameters][nfcore-parameters]
* [GA4GH Workflow Execution Service][ga4gh-wes]

# 10. Future pipeline integration ideas

Additional ideas for integrating pipelines in the future can be found in the [IRIDA Next pipeline integration ideas][iridanext-ideas] document.

# 11. Publishing guidelines

Our intention is to follow, as much as possible, the standards and practices set out by nf-core. However, we leave it as optional to actually publish pipelines/modules/subworkflows with the official nf-core repositories. We would encourage this where it makes sense in order to support re-use and giving back to the community (please refer to the [nf-core publishing requirements][] for the guidelines in this case). However, it is perfectly acceptible to publish pipelines/modules/subworkflows in separate Git repositories outside of nf-core. Please see the [if the guidelines don't fit][nf-core-external-development] and [using nf-core components outside of nf-core][nf-core-outside-nf-core] sections of the nf-core documentation for more information on this scenario and other locations to list your pipeline.

# 12. Legal

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
[nf-validation samplesheet]: https://nextflow-io.github.io/nf-validation/nextflow_schema/sample_sheet_schema_specification/
[nf-validation fromSamplesheet]: https://nextflow-io.github.io/nf-validation/samplesheets/fromSamplesheet/
[iridanext-samplesheet]: ideas.md#samplesheets
[iridanext-ideas]: ideas.md
[pha4ge-pipeline-standards]: https://github.com/pha4ge/pipeline-resources/blob/main/docs/pipeline-standards.md
[pipeline-tutorial]: https://github.com/apetkau/nf-core-assemblyexample
[nf-core-add-pipeline]: https://nf-co.re/docs/contributing/adding_pipelines
[nf-core-requirements]: https://nf-co.re/docs/contributing/guidelines#requirements
[gov-canada-open-source-software]: https://www.canada.ca/en/government/system/digital-government/digital-government-innovations/open-source-software/guide-for-publishing-open-source-code.html
[pha4ge-pipeline-security]: https://github.com/pha4ge/pipeline-resources/blob/main/docs/pipeline-standards.md#software-security-and-vulnerabilities
