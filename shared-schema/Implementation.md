# Shared Schema Implementation

1. Create a shared library for widely used scenarios
	- Start with start-navigate, page-view, data-classification, errors, metaData any other widely used, simple schemas
	- Create a [fake schema library](https://github.com/krogertechnology/schema-registry/pull/1809)
	- Make sure it works with Scenario Poet (WIP)
	- Implement the shared schema in the app schema libraries
		- We can do a schema at a time
		- We do not have to cutover all at once
1. Identify other high priority scenarios
	1. Determine how different apps use them, like add-to-cart
	1. Unify how those are defined
		- Most, if not all, properties will be optional unless we determine they are needed for the schema to make sense
		- Create property groups and data types where appropriate (ie splitting the different metadata field types out)
	1. Add newly composed schema to those schema librarises
		- Identify properties that need to be required per platform and update the `required` array accordingly
		- Shared properties can be 'overridden' inside apps' schemas by declaring them again inside the composed schema
			- One property that will always be overriden will be `schemaVersion`
1. Continue process for all remaining scenarios
 