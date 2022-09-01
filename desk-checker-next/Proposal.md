# Desk Checker Next

## Problem

With the Desk Checker (DC) tool we have issues with the requirements schema and collecting analytics payloads. 

### The Act of Desk Checking

With requirements schema, there is no real need to have the implementation team do any of the desk checking work. We need to enable a way for developers or SDETs to take their captured payloads and compare them against a provided requirements schema, identified by a JIRA ticket id and a requirement ID listed in the JIRA ticket.

We also need to document that the desk check was done, and the result of the desk check.

### Requirements Schema

The requirements schema is created in Data Dictionary (DD), downloaded, then attached to the JIRA ticket. There are issues with when a requirements schema should be created, how to keep it updated with the requirements in the ticket, and updated with Schema Registry (SR) updates. 

If the requirements of the ticket get updated, the Implementation team member must update the requirements schema, download it again, and re-attach it to the ticket. Hopefully the old requirements schema will be removed as well. This is a lot of book keeping on our part.

The schema in SR can also be updated based on another ticket as well. DC would, at the very least, throw an error around `schemaVersion`, but also throw errors around model or property changes that didn't exist when the requirements schema was created. We can get around this by creating a really slimmed down requirements schema that only has the properties that the ticket needs, and the scenario needs to be viable.

### Collecting Payloads

Developers seems to have an issue with collecting JSON payloads. They mostly don't know how to do it, so we would need guides to show them how to collect payloads from the browser, from Charles (native mobile), or from some other way of inspecting payloads from mobile devices.

Ground Kontrol made an app that intercepts calls from clients and have developers send the payload to a validation service.

## Proposed Solution

1. Service that saves one or more requirements schemas (RS) based on JIRA ticket
	- Can access requirements schema from URL
	- Can update requirements schema in one place as requirements change
	- Needs authentication and authorization for a user to modify a requirements schema
	- (??) Possibly save the requirements as a git branch to keep it updated with SR
		- We would have to keep schema registry from running all the CI/DI processes on these
		- Requirements schema would stay up to date with the latest version in SR
	- Requirements Schema based on JIRA ticket so it's easily findable
	- Service can accept a POSTed payload and return whether it passes validation 
	- Service can save each payload validation
	- Service can return the status of attempted validations for a JIRA ticket
1. Use Ground Kontrol's tool to intercept and capture payload data
1. UI updates to requirements schema creation
	- Modifying json schema in the text editor on Data Dictionary is not a great experience
	- Since we are only updating properties, can we have a UI that modifies property values?
	- Can be single acceptable value (const), or an array of acceptable values (enum), or no change
	- I do not want to get into modifying the allOf conditionals

### Break down

#### Switchboard payload capture (iOS at least)
1. When in debug mode, SDK sends a fully formed scenario + metadata to a debug telemetry relay to nowhere. 
	- We need the full formed payload to go through telemetry to intercept it
	- How do we have 1 relay for both V0 and V1?
	- Also useful for Ground Kontrol's tool
	- Could steal ground kontrol's implementation
1. Add a switch board view in the app that displays data flowing through telemetry relays
	- Filter and search the analytics relay payloads
1. Add ability to download data from the display in the switchboard
1. Teach DA Implementation how to download app builds from Firebase, access the Switchboard, access the payload list, and download payloads.

#### Ground Kontrol's Tool
1. Update the analytics SDKs (iOS and Android) to change when metadata is attached to payloads in telemeter
	- Right now this is done inside the SDKs themselves
	- Would we need to move it to scenario instantiation or can we do it in the relay? (must be at earliest the relay)
	- If we cannot do it in the relay, how can we capture metadata information via the tool?
	- In debug mode, can we send a fully formed payload (scenario + metadata) to a debug relay?
1. Tool needs to have the ability to download a json payload
1. Eventually figure out a mechanism for sending a payload to a service to be deskchecked (The Web Service item 2)
1. See how we can modify the web SDK to hook into the tool

#### The Web Service
1. Web service that accepts a RS and a payload and returns validation status as a HTTP Response body
	- We basically have this today but at [the desk checker](https://touchstone.katssx-prod.kpsazc.dgtl.kroger.com/desk-checker) so this provides little value
1. Service can save one more more RS tied to a jira ticket
	- Service can return validation status of a payload based on a RS
	- Do we need authentication for this?
		- We only want approved people uploading RS
	- Start adding value here
1. Service saves each payload submitted against a RS
	- We can query service for validation results by JIRA ticket

#### UI Update
1. Update the UI on Data Dictionary to support modifying the value of properties