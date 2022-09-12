# Schema Registry Change Management

I don't like this becuase it's built into, and thus dependent upon, a Schema Registry implementation

## The Plan

We can use Schema Registry change propagation of references to detect app libraries that will be affected by an update, a client change list. We can detect this both when the user updates and when the PR is opened into Schema Registry. We can do this for patch, minor, and major changes, but major changes are the ones that BDA clients need to care about.

For example, updating the Start-Navigate class would generate a list of users/app libraries that are affected by this update (in an ideal world, literally everyone). Updating the KHX Start-Navigate scenario would generate a client change list of only KHX.

We also have to account for consumer changes lists. Consumers care about specific scenarios in specific app libraries. Currently, our clients only care about specific scenarios inside Banner, but there will be need in the future to care about specific scenarios in other app libraries too. We can track app library:scenario name pairs, so `Banner:Filter-Item`, `Banner:Filter-And-Sort`, etc. We can track specific versioned IDs of schema, `/banner/scenarios/filter-and-sort/1.5.2`, `/banner/scenarios/internal-search/1.3.5`, etc. The latter would be similar to how our consumer manifests work right now. Each manifest tracks what specific scenario implementation is in a version of the SDK. Consumers could care to know what mapping (or mappings, since they generally have to keep multiple versions active at once) they have for specific versions of a scenario.

Consumers could be limited to tracking inherited classes, app library specific scenarios, and metadata wherever it lives. The consumers would be notified of _every_ change. We would need to track a point of contact for the consumer.

Like for consumers, we could implement a similar approach for Banner on a team level. We track what scenarios teams care about, and we could track the page name:component name (JIRA ticket id?) combination to help identify where the scenario they care about is used. We run into the issue of who maintains this. It would be a big task to ask front end teams to identify all their scenario implementations, so instead we could build these manifests over time as BDA Implementation works on requirements for analytics tickets. It would also need to track a point of contact for each front end team.

Similar to how the consumer manifest works, we would generate a list of interested parties on each update. Mass, important communication for major changes, and maybe lesser communication.

## Issues

Our change cadence is random at best. If we controlled changes to once a week, once a sprint, once a month, we could limit how much communication we actually do. This would inhibit speed of ticket completion, but it would also give BDA a predictable update cadence so we aren't almost spamming people with updates. Limiting update cadence could have the effect of limiting changes that go through too. We would have to plan and think about what changes we really want to have moving forward instead of just slamming PRs and saying we need approvals before the next release.s