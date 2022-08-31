# Breaking Changes 

## Problem

How can we support breaking changes to schema models without causing SDK updates to applications to cause build problems and delay deployments to production?

## Solutions

There are essentially two ways to handle this: one model per scenario, and multiple versioned models per scenario. Every one of these possible solutions still requires communcation of the changes, deprecation timelines, enforcing the deprecation. We need change management, which is outside the scope of this document. This is document focuses on Schema Registry, Scenario Poet, and the SDKs generated from them.

Please see [The Breaking Changes Documentation](https://confluence.kroger.com/confluence/display/BDA/Breaking+Changes) for what constitutes breaking changes in our space.

---

### One Model Per SDK

This is very similar to how we operate now. We have one model per scenario per SDK. We handle breaking changes as told in [The Breaking Changes Documentation](https://confluence.kroger.com/confluence/display/BDA/Breaking+Changes) which boils down to:

	We make a non-breaking change with a note and communcation that the breaking change will go live at a certain specified date. After the specified date, we make the breaking change.

Front end teams will be told in the communcation what the change is and when the change will go live. It will be their responsibility to determine if the change affects them, and how they plan to address it. After the official date specified in the communication, the SDK updates will introduce the breaking change and if it causes problems they must address it.

#### What do we need to make this work

We need to determine how we document breaking changes in Schema Registry. We have to support deprecations and new required additions. Deprecations include removing a property, removing an enumerated value, removing a scenario, removing 'something'. We will need to add support to Schema Registry to document these changes. We also need to support the addition of required properties, another breaking change.

We would need to document the property affected, when the change will officially take effect, what the change is. We will document the 'why' elsewhere, but we can link to the 'why' the what of the change.

We need to update Scenario Poet to support the new deprecation information from Schema Registry to provide the information about what is happening to developers.

The breaking changes in the SDKs will show up as build warnings in the applications until the official date, then the

#### Advantages

We have but one version of every scenario, which is easy for requirements.

SDK generation and implementation of scenarios will be significantly easier conceptually. There is only one version of a scenario in a SDK.

It's very easy to focus on generating one SDK with one version of scenarios.

#### Disadvantages

We cannot force front end teams to update their SDKs. We can generate new versions all day, but if they do not play the work to introduce those new versions, they will not get the build warnings for the upcoming changes. Even after we make the breaking change live, if the front end team decides not to update, they will never get the change.

This will get complicated to manage if we have multiple upcoming breaking changes on a model at the same time. There would be multiple warnings for a single scenario that will not be clear that there are multiple breaking changes being introduced.

Updating Scenario Poet to support various breaking changes to provide good warnings to developers will be complicated. Specifically, how we handle the addition of a required property. We'd need multiple initializers, the current, deprecated one that has a warning, and a new one that has the new now-optional-but-soon-required property that would need to be required for the initializer, but optional on the model.

---

### Multiple Supported Versions of SDK

This solution would have us maintain multiple active versions of scenarios. We would maintain every major version with minor and patch updates and these updated scenarios would go into a version of the SDK. Each version of the SDK would have at least one major version scenario difference.

For Example:

_SDK 1.2.3_
- Start-Navigate v1
- Page-View v2
- Add-to-Cart v1
- Add-to-List v3

_SDK 2.0.1_
- Start-Navigate v1
- Page-View _v3_
- Add-to-Cart _v2_
- Add-to-List v3

These can be broken down into SDK V1 and SDK V2. The only difference between them would be the Page-View major version difference. We would apply bug fixes and changes to Page-View v2 as needed, and Page-View v3.

If we decide that Page-View v2 will no longer be suppored, we can do our deprecation and change management process to mark the entire scenario as deprecated.

#### What do we need to make this work

Schema Registry would need to be updated to support version bumping off of models other than latest. Schema Registry would need to not allow creating new major versions off of versions other than the latest. A new major must always come from the previous major, not the previous previous major.

We would need Scenario Poet to support pulling in, and using, Schema Registry versions for SDK generation instead of the latest version that we use now.

Those who generate SDKs would need to be aware of what version of Schema Registry they are targetting for their update.

#### Advantages 

Breaking changes are essentially entirely optional. We would maintain various major versions indefinitely.

#### Disadvantages

SDK updates become complicated. The instigator of the update must know the major version they are want for their app.

Model updates become complicated. We no longer update the latest model, we update the model that the front end team is using in their application.

We need to make sure any minor change to Page-View v2 would be applied to v3 as well. We do not want them drifting apart and providing different functionality. Two model updates for every update we want to apply.

We can't force front end teams to migrate if they choose not to migrate.

Multiple versions of one library in an application is not widely supported, so this may not be technically possible. So this means that if a developer using SDK v1.2.3 wants access to Add-to-Cart v2, but not Page-View v3, they cannot have Add-to-Cart v2 without also updating to Page-View v3.

---

### One SDK Built From Chosen Versions of Scenarios

This solution would allow front end teams to explicitly choose what version of scenarios they want when their SDK is generated.

#### What do we need to make this work

Schema Registry would need to be updated to support version bumping off of models other than latest. Schema Registry would need to not allow creating new major versions off of versions other than the latest. A new major must always come from the previous major, not the previous previous major.

SDK generation would need to support generating scenarios of the front end team's choice. They would have to be aware of what version of one or more scenarios they want to build. If they don't specify a version, we can assume they want the latest.

We would need the deprecation dates of older versions scenarios, if we decide that older versions of scenarios are no longer valid.

When a front end team decides to use a version of a scenario that we deem too old, we would need to fail the generation process and let them know. This would also force them to update to the latest version.

Scenario Poet could no longer use shared models, like a product model, across multiple scenarios since we can no longer garauntee that a model will be the same across different major versions of scenarios that share that model.

#### Advantages 

Very customizable.

Front end teams can choose when they want to migrate at their own time.

Shared generated models would still be supported. If a shared model is major version bumped, all models that reference it are also major version bumped, thus still sharing that model.

#### Disadvantages

Front end teams can choose when they want to migrate at their own time, potentially not migrating at all.

We could be maintaining multiple versions of one or more scenarios at the same time.

---

### One SDK With Multiple Versioned Models

Here we would have one SDK, but the SDK would come with explicitly major versions of the scenarios. Each previous version would be marked deprecated with all the information we decide.

_SDK 1.2.3_
- Start-Navigate-V1
- Page-View-V2
- Add-to-Cart-V1
- Add-to-List-V3

_SDK 2.0.1_
- Start-Navigate-V1
- Page-View-V2 (deprecation warning)
- Page-View-V3
- Add-to-Cart-V1 (deprecation warning)
- Add-to-Cart-V2
- Add-to-List-V3

After a certain specified date unique to each deprecated scenario, the deprecated scenarios will be removed from the SDK. I am unsure if this would constitute a major version change.

#### What do we need to make this work

We would need to update Scenario Poet to generate all models with the `-V#` appended to the end. We cannot just have a `StartNavigate` scenario anymore, since if we bump to the next major, all implementations will be broken. 

I assume we would not want to keep previous major versions supported, so we would only apply all updates to the latest major of each scenario.

We don't have to worry about marking specific fields as deprecated, we would do the latest version of the previous major as deprecated.

#### Advantages 

#### Disadvantages

---

### One SDK With Multiple Versioned SDKs

#### What do we need to make this work

#### Advantages 

#### Disadvantages

---

### One SDK Built From Chosen Versions of Scenarios

#### What do we need to make this work

#### Advantages 

#### Disadvantages