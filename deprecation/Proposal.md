# Deprecating Scenarios and Properties

## Problem

How can we deprecate, then remove, entire scenarios and individual fields without breaking everything?

## Proposed Solution

- Don't do anything while V0 exists for Banner

- Deprecation support in Schema Registry (SR)
	- [deprecated keyword](http://json-schema.org/draft/2019-09/json-schema-validation.html#rfc.section.9.3). Update the description of the item being deprecated with the appropriate information.
	- Throws a warning when building
	- Can include things like why and a date when it will go away
	- Includes communication to PMs with the same information
	- Do not want to hunt down who owns what
	- After date passes, we remove property from SR, and just delete the code in weekly updates
- Isolate deprecated fields into a `legacy` object, keep SR deprecation and code gen support
	- Would be straight forward for code gen and migration
	- Obvious it will be going away
	- Can update adobe transforms to support the property movement
- Multiple versions maintained in SDKs
	- How do we handle this with name collisions?
	- Can we do something with constructors?