# Desk Checker Next

## Problem

With the Desk Checker (DC) tool we have issues with the requirements schema and collecting analytics payloads. 

### Requirements Schema

The requirements schema is created in Data Dictionary (DD), downloaded, then attached to the JIRA ticket. There are issues with when a requirements schema should be created, how to keep it updated with the requirements in the ticket, and updated with Schema Registry (SR) updates. 

If the requirements of the ticket get updated, the Implementation team member must update the requirements schema, download it again, and re-attach it to the ticket. Hopefully the old requirements schema will be removed as well. This is a lot of book keeping on our part.

The schema in SR can also be updated based on another ticket as well. DC would, at the very least, throw an error around `schemaVersion`, but also throw errors around model or field changes that didn't exist when the requirements schema was created. We can get around this by creating a really slimmed down requirements schema that only has the fields that the ticket needs, and the scenario needs to be viable.

### Collecting Payloads

Developers seems to have an issue with collecting JSON payloads. They mostly don't know how to do it, so we would need guides to show them how to collect payloads from the browser, from Charles (native mobile), or from some other way of inspecting payloads from mobile devices.

I will be ignoring this since we can't tool our way out of this.

## Proposed Solution

1. Service that saves a requirements schema (RS) based on JIRA ticket
	- Can access requirements schema from URL
	- Can update requirements schema in one place as requirements change
	- Needs authentication and authorization for a user to modify a requirements schema
	- (??) Possibly save the requirements as a git branch to keep it updated with SR
		- We would have to keep schema registry from running all the CI/DI processes on these
		- Requirements schema would stay up to date with the latest version in SR
	- Requirements Schema based on JIRA ticket so it's easily findable
1. DC tool will be modified to accept a jira ticket id, and a payload body. 
	- Query the above service to get requirements schema
	- Potential to save different validation attempts for posterity (payload and RS at submition time)
	- Would be able to see what payloads passed/did not pass/if it passed at all