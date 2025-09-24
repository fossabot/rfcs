# Meta
[meta]: #meta
- Name: Declarating layers for inline buildpacks
- Start Date: 2025-09-24
- Author(s): [@jkutner](https://github.com/jkutner)
- Status: Draft <!-- Acceptable values: Draft, Approved, On Hold, Superseded -->
- RFC Pull Request: (leave blank)
- CNB Pull Request: (leave blank)
- CNB Issue: (leave blank)
- Supersedes: (put "N/A" unless this replaces an existing RFC, then link to that RFC)

# Summary
[summary]: #summary

Introduce new fields in `project.toml` that enable layer metadata for inline buildpacks.

# Definitions
[definitions]: #definitions

* inline buildpack - a buildpack defined in the same repo as the app it will build (see [RFC-0048](https://github.com/buildpacks/rfcs/blob/main/text/0048-inline-buildpack.md))

# Motivation
[motivation]: #motivation

Inline buildpacks are very powerful, but they're simplicity is undermined by the complexity of dealing with the Buildpack API for creating layers and defining layer metadata.

# What it is
[what-it-is]: #what-it-is

Introduce a delcarative mechanism for defining layers and layer metadata that will be materialized for the inline buildpack.

# How it Works
[how-it-works]: #how-it-works

Introduce a new table within the `project.toml` descriptor at `[[io.buildpacks.group.script.layer]]` with the following keys:
* `name` - the name of layer, used as path name
* `launch` - corresponds to `launch` key in [layer metadata](https://github.com/buildpacks/spec/blob/main/buildpack.md#layer-content-metadata-toml)
* `build` - corresponds to `build` key in [layer metadata](https://github.com/buildpacks/spec/blob/main/buildpack.md#layer-content-metadata-toml)
* `cache` - corresponds to `cache` key in [layer metadata](https://github.com/buildpacks/spec/blob/main/buildpack.md#layer-content-metadata-toml)

Before an inline buildpack is executed as part of a build, the platform creates the layer(s) defined by the `layer` table, and the associated layer metadata file.

Example:

```toml
[[io.buildpacks.group]]
id = "hello-buildpack"

    [io.buildpacks.group.script]
    api = "0.10"
    layers = [{id = "hello", launch = true}]
    inline = """#!/bin/bash
set -euo pipefail

touch $1/hello/hello.txt
echo "hello" > $1/hello/hello.txt
"""
```

# Migration
[migration]: #migration

This change is backward compatible.

# Drawbacks
[drawbacks]: #drawbacks

* may create unusual conflicts between code that edits/creates layer metadata in the inline script
* may require breaking a seperation of concerns to have pack write the layer metadata and create directories

# Alternatives
[alternatives]: #alternatives

- Provide helper scripts to the inline buildpack to make common operations easier

# Prior Art
[prior-art]: #prior-art

- [RFC-0048: Inline buildpacks](https://github.com/buildpacks/rfcs/blob/main/text/0048-inline-buildpack.md)

# Unresolved Questions
[unresolved-questions]: #unresolved-questions

- What should the default launch, build, and cache values be for an inline layer

# Spec. Changes (OPTIONAL)
[spec-changes]: #spec-changes

[Project Descriptor](https://github.com/buildpacks/spec/blob/main/extensions/project-descriptor.md)

```toml
[[io.buildpacks.group.script.layer]]
name = "<string>"
launch = false
build = false
cache = false
```

# History
[history]: #history

<!--
## Amended
### Meta
[meta-1]: #meta-1
- Name: (fill in the amendment name: Variable Rename)
- Start Date: (fill in today's date: YYYY-MM-DD)
- Author(s): (Github usernames)
- Amendment Pull Request: (leave blank)

### Summary

A brief description of the changes.

### Motivation

Why was this amendment necessary?
--->