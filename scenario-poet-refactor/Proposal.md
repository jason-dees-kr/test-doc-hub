# Scenario Poet Refactor

## Problem

Scenario Poet (SP) is our way of generating analytics models for all our development projects. For the most part, it is untested, which is bad for a tool actively used in production. 

SP is very opinionated about schema, enforcing analytics rules and failing when those rules do not match. We can make SP less opinionated to help spread its use among other teams at kroger. We can update SP to simply read in schema and output generated models for a given language.

## Proposed Solution

- Add unit testing to Core SP
- Add output testing to Kotlin generation
- Refactor SP to not do analytics model validation
- Refactor SP to allow arbitrary schema directory lay out

### Add unit testing to Core SP

Before we do any other work, we need to write tests to verify that what we have currently works properly. Unit tests need to be added to the core frameworks of SP, which include readinng schema, parsing schema, inflating properties, and, generally, how the models are created for sending to the various writers.

This allows us to validate current functionality, and gives us confidence that future changes won't break other parts of the project. Once we have that, we can start working on refactoring and testing SP.

## Add output testing to Kotlin generation

Only the Swift and TypeScript writers have output testing currently, which means those writers have tests that verify a file is built in the expected format. The Kotlin writer should have this as well so that we can confidently refactor and update the Kotlin writer without fear of breaking code generation.

## Refactor SP to not do analytics model validation

SP is opinionated about what fields should be in a schema. We should make SP care less about how the schema it is reading is structured. This will give us more flexibility in how we construct schemas in Schema Registry (SR). This will also allow SP to be used more widely in Kroger as a whole for generating consistent models across multi-platform, client-server apps.