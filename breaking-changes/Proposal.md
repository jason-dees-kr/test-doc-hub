# Breaking Changes 

## Problem

How can we support breaking changes to schema models without causing updates to fail?

## Proposed Solution

All of these include communication to stakeholders, on a per app basis. All banner teams, all instock teams, all KHX teams, etc. As we implement inherited schema across Kroger, these changes will cascade to more and more teams. Inherited schema actually makes this _harder_.

We should come up with a public change policy that we can point users towards. We should define what constitutes a `breaking change` and a `non-breaking change`.

Information would include:

- What is happening
	- Scenario, property, enumerated value being changed, removed, required 
- Why it is happening
	- The value this brings to the app teams individually and Kroger as a whole
- Date that the change will fully take effect
	- Unless otherwise noted, this change will have to be done over a long period of time
	- There can be exceptions for stuff like PII/PHI data, immediate reporting needs delegated from on-high

### One Scenario Version 

This implies communication of the change with the information listed above.

This does not introduce a breaking change immediately. We make the change initially but do not make the change required yet.
- __Adding a required property__: We add the property as not required, but somehow communcate to devs that it will be required. Make property required after time period has passed.
	- Not sure how to document or code gen this. Two constructors, one with a deprecation warning?
- __Removing a property__: Make it not required (if it is required), mark the property as deprecated in the schema, code gen will put a warning around that property. After specified time period has passed, remove property from schema.
	- Again, 2 constructors, one with a warning and one with no warning and no removed property?
- __Renaming a property__: Add a new optional property with the correct name, mark old incorrectly named property as deprecated. After time period has passed, remove incorrectly named property from the schema.
- __Removing enumerated value__: Mark value as deprecated. Code gen will provide warnings in the apps around this
- __Renaming enumerated value__: Add correctly named value, mark incorrectly named value as deprecated. Remove incorrect value after time period has passed.
- __Changing property type__: pfffttttt who knows don't do this. How do we change a property type and keep the same name as the previous property?
- __Remove Scenario__: Mark entire scenario as deprecated. Remove after deprecation period has passed.

### Multiple Versions in SDK

This includes communcation.

#### Version off of Scenario Version
Inside the SDKs we would support the latest version, and any previous deprecated major version that is within its deprecation window. We would have StartNavigate-V1, StartNavigate-V2, StartNavigate-V3 (latest) while V1 and V2 are within their deprecation window. Once out of deprecation window, they are no longer generated.

We cannot do implicit latest versions since any future versions would cause breaking changes. We would have to have a typed version of every active version of every scenario. 

A Sample SDK could include:
- StartNavigate-V1 (Within deprecation window)
- StartNavigate-V2 (Within deprecation window)
- StartNavigate-V3 (Latest)
- PageView-V1 (Latest, no other versions in deprecation window)
- StartFlow-V2 (Latest, no other versions in deprecation window)
- AddToCart-V1 (Latest, no other versions in deprecation window)
- AddToList-V3 (Latest, no other versions in deprecation window)
- ViewProduct-V5 (Within depecation window)
- ViewProduct-V6 (Latest)

#### Version off Schema Registry Version

We could also version SDK models on Schema Registry releases. Old major versions of Schema Registry will get a deprecation date. This would create a V9.StartNavigate, even if it is the same as V8.StartNavigate (which would now be marked as deprecated, along with every other model in the V8 namespace/folder).

Name space versioning is much more straight forward, but every major essentially deprecates every current scenario in the app.

We will also major version bump from other apps. If banner gets a major version bump a scenario, it will mark every other schema root as having a version bump next code-gen update.

A Sample SDK Could include:
- V9 (Deprecated)
	- StartNavigate
	- PageView 
	- StartFlow 
	- AddToCart 
	- AddToList 
- V10 (Deprecated)
	- StartNavigate
	- PageView 
	- StartFlow 
	- AddToCart 
	- AddToList 
- V11 (Latest)
	- StartNavigate
	- PageView 
	- StartFlow 
	- AddToCart 
	- AddToList 