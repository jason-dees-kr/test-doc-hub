# Shared Schema Implementation

1. Make a library of all properties and the following attributes.
	1. name
	1. type
	1. description
	1. partition(s) to which they belong
	1. scenarios they exist in
	- What about `componentName`?
	- How do we handle `name`? `product.name`, 
	- What about `dataClassification` for KHX that has different values than the others? Force unification?
	- I'm assuming no custom properties will exist
		- All properties will exist as a schema
		- A scenario will add a property/property group to its schema
1. Create a shared library called "properties" and put the schema for each property there.
	- This is a partition of only property groups
1. Create property groups from those properties based on their attributes.
	- Determine partition for property groups too
1. Create data types from those property groups.
	- Determine partition for data types too
1. Create classes from those data types and property groups
	- Only fields in a class will be `scenarioName` and `schemaVersion`
1. Start migrating over scenarios to using classes, data types, and property groups