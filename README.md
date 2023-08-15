# Nextflow pipeline specification

*This document is in-progress and should be considered as a **DRAFT** that is subject to change at any time.*

This document describes the specification for developing Nextflow pipelines. This follows many of the recommendations from the [nfcore pipeline schema][nfcore-pipeline-schema].

# Input

Input for pipelines follows the [nfcore parameters][nfcore-parameters] specification. In particular, input will be passed via a CSV file listing samples and reads (using `--input`) as described in <https://nf-co.re/viralrecon/2.6.0/usage>.

# Resource requirements

To define computational resource requirements for each process, we will follow the [nf-core][] standards as much as possible, where resources are adjusted by a `label` in each `process` of a pipeline. Defining these resources will be divided into these two roles:

* **Pipeline developer**: Responsible for setting an appropriate `label` in each process of the NextFlow pipeline to appropriatly define required resources.
* **System administrator**: Responsible for defining and maintaining the mapping between a `label` and the specific resources required by a pipeline.

## Pipeline developer

The pipeline developer will add a `label` to each `process` of a NextFlow pipeline to adjust resources. For example:

```
process name {
    label 'process_single'

    // ...
}
```

### Accepted resource labels

The following labels will be accepted:

* `proccess_single`
* `process_low`
* `process_medium`
* `process_high`
* `process_very_high`: An addition over those provided by nf-core.

## System administrator

The system administrator is responsible for maintaining a mapping between the `label` and the specific resources required by a pipeline.

# Output

Output will consit of a JSON file specifying metadata to associate with each sample:

**output-metadata.json**:
```json
{
    "sample1": {
        "field1": "value",
        "field2": "value",
    }
}
```

# Executing pipelines

Pipelines will be executed via the [GA4GH Workflow Execution Service][ga4gh-wes] API.

# Resources

* [nfcore pipeline schema][nfcore-pipeline-schema]
* [nfcore parameters][nfcore-parameters]
* [GA4GH Workflow Execution Service][ga4gh-wes]

# Publishing guidelines

Our intention is to follow, as much as possible, the standards and practices set out by nf-core. However, we leave it as optional to actually publish pipelines/modules/subworkflows with the official nf-core repositories. We would encourage this where it makes sense in order to support re-use and giving back to the community (please refer to the [nf-core publishing requirements][] for the guidelines in this case). However, it is perfectly acceptible to publish pipelines/modules/subworkflows in separate Git repositories outside of nf-core. Please see the [if the guidelines don't fit][nf-core-external-development] section of the nf-core documentation for more information on this scenario and other locations to list your pipeline.

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
