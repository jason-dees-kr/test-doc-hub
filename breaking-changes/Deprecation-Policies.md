# Deprecation Policies

## [Kubernetes](https://kubernetes.io/docs/reference/using-api/deprecation-policy/)

They have a robust alpha-beta-release lifecycle that we will probably not support. I am focusing on major releases. Our `-pre` pre-release branches are probably closest to their beta releases.

- Once an API element has been added to an API group at a particular version, it can not be removed from that version or have its behavior significantly changed, regardless of track.


- For example, an object can be written as v1 and then read back as v2 and converted to v1, and the resulting v1 resource will be identical to the original. The representation in v2 might be different from v1, but the system knows how to convert between them in both directions. Additionally, any new field added in v2 must be able to round-trip to v1 and back, which means v1 might have to add an equivalent field or represent it as an annotation.
	- This implies we should do lots of mappings in Clickstream to and from various versions. This is a lot of work that we would not want to do.

- GA API versions may be marked as deprecated, but must not be removed within a major version of Kubernetes
	- They keep two majors versions live at a time, current and previous deprecated

- Users must be able to upgrade to a new release of Kubernetes and then roll back to a previous release, without converting anything to the new API version or suffering breakages (unless they explicitly used features only available in the newer version). This is particularly evident in the stored representation of objects.
	- I'm not sure how they implement breaking changes in APIs if every version has to be backwards compatible with the previous version with no changes.

- Metrics, for the corresponding stability class, must function for no less than: STABLE: 4 releases or 12 months (whichever is longer)
	- They are guaranteeing that a metric will exist for at least a year
- Metrics, after their announced deprecation, must function for no less than: STABLE: 3 releases or 9 months (whichever is longer)
	- But after the announced deprecation of the metric, it must exist for another 9 months

## [Currency Cloud](https://developer.currencycloud.com/deprecation-policy/)

This policy is 3 months from the time the feature was marked as deprecated. We at Kroger can probably enforce tighter deadlines like this.

No more development on the deprecated item. This could mean that we keep old versions of scenarios around for X months to give people a chance to update, but then remove them at the deadline.

## [IBM](https://www.ibm.com/docs/en/security-verify?topic=apis-api-compatibility-policy-deprecation-policies)

IBM seems to do non-breaking changes at will, which will work for Kroger. 

IBM has a 12 month deprecation timeline. Release notes contain the specific date

## [Blackboard](https://docs.blackboard.com/legal/deprecation-policy)

If Blackboard needs to make a change that is not backwards compatible, they release a object/resource/media type. This would be like us creating a new start-navigate everytime we need release a breaking start-navigate change.

They version APIs' URI endpoints. They maintain deprecated APIs for at least 1 year.

## [Canva](https://docs.developer.canva.com/apps/platform-concepts/deprecation-policy)

Canva has a 6 month public, stable API deprecation policy, unless there is a reason to make it shorter, like security issues.

They have definitions for breaking and non-breaking changes. Kroger could adopt something similar since Canva's will apply to our model changes and updates.	

## [LinkedIn](https://docs.microsoft.com/en-us/linkedin/shared/breaking-change-policy?view=li-lms-2022-08)

LinkedIn announces when a breaking change will be introduced and how to migrate. They do not announce non-breaking changes.

## [Index Exchange](https://kb.indexexchange.com/rti_providers/reporting/breaking_change_policy_rti_providers.htm)

Their policy is to support breaking changes for 60 days, like just under 2 months. The shortest period yet.