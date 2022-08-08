# Shared Schemas Proposal

## Problem
Many schema across are very similar, if not exactly the same, across the analytics schema libraries. The schemas are all currently do not share any references but do share many of the same fields. When we want to update a schema, such as `start-navigate`, for all the applications, we must update every `start-navigate` schema individually. This means either many small change pull requests, or one large change pull request, both of which are onerous to create and review.

## Proposed Solution
Inside Schema Registry we create a `shared` directory, partitioned into sections by how a schema is used. Each scenario will have a base `class` in the `shared` directory that will represnt what Kroger views as a viable version of the sceario. Each app schema library will have a scenario implementation that references a `shared` class.

We can leverage functionality built into Schema Registry to automatically version bump every referencing schema of a shared schema. This allows us to update a single shared schema and have that update propagate to any schema that references it. All schemas will then be aligned and updated with one change.