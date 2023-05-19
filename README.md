# Nextflow pipeline specification

*This document is in-progress and should be considered as a **DRAFT** that is subject to change at any time.*

This document describes the specification for developing Nextflow pipelines. This follows many of the recommendations from the [nfcore pipeline schema][nfcore-pipeline-schema].

# Input

Input for pipelines follows the [nfcore parameters][nfcore-parameters] specification. In particular, input will be passed via a CSV file listing samples and reads (using `--input`) as described in <https://nf-co.re/viralrecon/2.6.0/usage>.

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
[nfcore-parameters]: https://nf-co.re/docs/contributing/guidelines/requirements/parameters
[ga4gh-wes]: https://ga4gh.github.io/workflow-execution-service-schemas/docs/
