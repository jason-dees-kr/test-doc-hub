# Breaking Changes 

## Problem

How can we support breaking changes to schema models without causing updates to fail?

## Proposed Solution

All of these include communication to stakeholders, on a per app basis. All banner teams, all instock teams, all KHX teams, etc. As we implement inherited schema across Kroger, these changes will cascade to more and more teams. Inherited schema actually makes this _harder_.

We should come up with a public change policy that we can point users towards. We should define what constitutes a `breaking change` and a `non-breaking change`.

Information would include:

	- What is happening
		- Scenario, field, enumerated value being changed, removed, added (if required)
	- Why it is happening
		- The value this brings to the app teams individually and Kroger as a whole
	- Date that the change will fully take effect
		- Unless otherwise noted, this change will have to be done over a long period of time
		- There can be exceptions for stuff like PII/PHI data, immediate reporting needs delegated from on-high

- Don't do anything while V0 exists for Banner
- How do we mark specific values in an enum as deprecated?
	- we can do field and scenario level, but individual values are tough
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

### Add new fields, mark old fields as deprecated with an end date
	- Includes communication, what is happening, why 
	- No immediate breaking changes
	- Alerts developers to the issue

### Multiple Versions in SDK
- Allows us to modify schema at-will with no worry or regard to how it will affect developers
- If a team that is using an old version has a new measurement plan for the same scenario, do we support the old version with a newer version of it?
	- Go from view-product 1.2.3 to view-product 1.2.4, where latest view-product is 2.4.0
	- We do not support that right now
- We would have to support sunsetting old versions so we don't keep them around forever
	- Still have to come up with a migration plan
	- The old version would be 'deprecated' and we would still have to migrate people over to the newest version within a timeline
- Maintain a list for how long a version of a scenario will exist and only put those into the SDK
	- After a certain date, remove those versions
	- Do we start version-name-spacing every scenario version?
		- StartNavigate-v1_3_4, StartNavigate-1_5_0

## Pitfalls

- Maintaining legacy reports
	- how long do we keep those active and maintained
- Adobe reporting re use of previously used, but now unused, variables can cause issues for users with visitors from the before times.
	- Filter update trying to use list2, but list2 also being for laf data, so we started collecting laf data in adobe again.