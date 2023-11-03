# IRIDA Next Nextflow pipeline specification

*This document is in-progress and should be considered as a **DRAFT** that is subject to change at any time.*

This document describes the specification for developing IRIDA Next Nextflow pipelines. This follows many of the recommendations from the [nfcore pipeline schema][nfcore-pipeline-schema].

# 1. Input

Input for pipelines follows the [nfcore parameters][nfcore-parameters] specification. In particular, input will be passed via a CSV file listing samples and reads (using `--input`).

## 1.1. Default samplesheet

The default samplesheet looks like:

| sample  | fastq_1      | fastq_2      |
|---------|--------------|--------------|
| SampleA | file_1.fq.gz | file_2.fq.gz |

## 1.2. Modify samplesheet

In order to modify the samplesheet structure, please follow the [nf-validation samplesheet schema specification][nf-validation samplesheet]. This involves modifying the `assets/schema_input.json` JSON schema defining the expected fields (columns) in a samplesheet and configuring your pipeline to use [nf-validation fromSamplesheet][nf-validation fromsamplesheet] to create a channel of input data for your pipeline.

# 2. Resource requirements

To define computational resource requirements for each process, we will follow the [nf-core][] standards as much as possible, where resources are adjusted by a `label` in each `process` of a pipeline. Defining these resources will be divided into these two roles:

* **Pipeline developer**: Responsible for setting an appropriate `label` in each process of the NextFlow pipeline to appropriatly define required resources.
* **System administrator**: Responsible for defining and maintaining the mapping between a `label` and the specific resources required by a pipeline.

## 2.1. Pipeline developer

The pipeline developer will add a `label` to each `process` of a NextFlow pipeline to adjust resources. For example:

```
process name {
    label 'process_single'

    // ...
}
```

### 2.1.1. Accepted resource labels

The following labels will be accepted:

* `proccess_single`
* `process_low`
* `process_medium`
* `process_high`
* `process_very_high`: An addition over those provided by nf-core.

## 2.2. System administrator

The system administrator is responsible for maintaining a mapping between the `label` and the specific resources required by a pipeline.

# 3. Output

## 3.1. Files

Output files will be placed into a directory specified by the `--outdir` parameter. For examples given below we assume `--outdir output` was used.

## 3.2. IRIDA Next JSON

An `output/iridanext.output.json.gz` file will be written, which specifies the files and metadata to store into IRIDA Next. This file will look like:

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

### 3.2.1. Output files

The `files` section lists the files to store within IRIDA Next (files not listed here will not be stored). This consists of two different sections:

#### 3.2.1.1. global

The `global` section defines a list of files associated with the pipeline globally. Each file entry is a JSON object with a single key `path` defining the path to the file (relative to the `output` directory). More keys may be added in the future.

```json
"global": [
    {"path": "summary.txt"}
]
```

#### 3.2.1.2. samples

The `samples` section defines a list of files to be associated with each sample individual (e.g., genome assemblies). Each sample identifier should correspond to the same identifer specified in the `--input` samplesheet. Each file entry is a JSON object with a single key `path` defining the path to the file (relative to the `output` directory).

```json
"samples": {
    "SampleA": [
        {"path": "assembly/assembly.fa.gz"}
    ]
}
```

### 3.2.2. Metadata

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

#### 3.2.2.1. Simplified metadata JSON

The simplified metadata JSON consists of only a single level of key-value pairs under each `sample` in the metadata object. This will be used to simplify integration of metadata with IRIDA Next prior to support for more complicated data structures.

The below is the equivalent simplified JSON from the version defined in [3.2.2. Metadata](#322-metadata).

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

# 4. Executing pipelines

Pipelines will be executed via the [GA4GH Workflow Execution Service][ga4gh-wes] API.

# 5. Resources

* [nfcore pipeline schema][nfcore-pipeline-schema]
* [nfcore parameters][nfcore-parameters]
* [GA4GH Workflow Execution Service][ga4gh-wes]

# 6. Publishing guidelines

Our intention is to follow, as much as possible, the standards and practices set out by nf-core. However, we leave it as optional to actually publish pipelines/modules/subworkflows with the official nf-core repositories. We would encourage this where it makes sense in order to support re-use and giving back to the community (please refer to the [nf-core publishing requirements][] for the guidelines in this case). However, it is perfectly acceptible to publish pipelines/modules/subworkflows in separate Git repositories outside of nf-core. Please see the [if the guidelines don't fit][nf-core-external-development] and [using nf-core components outside of nf-core][nf-core-outside-nf-core] sections of the nf-core documentation for more information on this scenario and other locations to list your pipeline.

# Legal

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
[nf-core]: https://nf-co.re/
[nfcore-parameters]: https://nf-co.re/docs/contributing/guidelines/requirements/parameters
[ga4gh-wes]: https://ga4gh.github.io/workflow-execution-service-schemas/docs/
[nf-core publishing requirements]: https://nf-co.re/docs/contributing/guidelines#requirements
[nf-core-external-development]: https://nf-co.re/docs/contributing/guidelines#if-the-guidelines-dont-fit
[nf-core-outside-nf-core]: https://nf-co.re/docs/contributing/tutorials/unofficial_pipelines
[nf-validation samplesheet]: https://nextflow-io.github.io/nf-validation/nextflow_schema/sample_sheet_schema_specification/
[nf-validation fromSamplesheet]: https://nextflow-io.github.io/nf-validation/samplesheets/fromSamplesheet/
