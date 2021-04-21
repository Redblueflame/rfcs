# Summary

This RFC aims to add a step in the lifecyle of any API or module, called `experimental phase`. It explains in more details the broad description present in the [RFC 9](https://github.com/QuiltMC/rfcs/pull/9).


# Motivation

An experimental stage allows modders and the API developers to test out features present in the standard library before they are officially made available, ensuring the quality of the implementation once they reach the GA stage.

# Explanation

The experimental system doesn't work the same way for both modules and APIs.

**NOTE:** For readability, experimental modules and API are simplified as experimental code in this RFC.

## Definitions

### **Toggle**

A toggle is a change in configuration that, once applied, implicitly or explicitly enable the use of an experimental feature or module. This can be a normalized solution (see the **normalized toggle**) or any other change, like the following, but not limited to:

- Switching versions of a dependency **manually**
- Changing a compiler option (adding a CLI flag)
- Adding an environment variable

### **Normalized Toggle**

A normalized toggle is a uniform and agreed-upon way to *toggle* some experimental features or modules.

### **Closed issue**

A closed issue is an issue that either resolved or dismissed due to one of the following reasons:
- Duplicate of another open or closed issue
- Already fixed in the **main** branch, a fix in a non-merged PR is not considered closed
- A non issue (An issue that is not one, usually due to design decisions)
- Out of scope (An issue that is not relevent to any of the experimental features linked)
- Neglected issue (An issue that did not get a response for at least 14 days after a question was asked)
- TODO: Add more closed reasons.

## Lifetime of experimental code

A module or API is considered experimental once they got merged in the main repository, and stays as one until its stabilization.

After merging, a new issue should be created in the repository, using the following format as title: `[Tracking Issue] {Name of feature} ({RFC ID})`
And the following template: 
```md
This is the tracking issue for #{RFC ID}, {Small description of the feature}.

TODOs:
{
    List of all issues still needed to stabilize the feature, using the checkbox format:
    - [ ] #{Issue ID} {Title of issue}
}

Blocking issues:
{
    List of all open issues about the feature, using the checkbox format:
    - [ ] #{Issue ID} {Title of issue}
}
```

Once every TODO and blocking issue is closed, the feature can be added to the next related working group meeting, where the team will vote on the stabilization of the feature. If the stabilization is refused, the team has to provide reasoning behind the choice, in addition to TODOs required to send the stabilization request again.

If the stabilization is accepted, the PR enters a grace period of (7) days, and if no new blocking issue appears, the stabilization PR can be merged.


Here is a schema of the whole process:

![Stabilization Flow Dragram](../attachments/xxxx-stabilization-flow.jpg)

## Differences of experimental code

In order to execute experimental code you need to do one of the following:
- Enable in the gradle settings the feature:
```gradle
// In the quilt build section
enableExperimentalFeatures 'xxx', 'yyy', 'zzz'
```
- Enable the feature for the current class using the following syntax
```java
@EnableExperimental("xxx", "yyy")
public class UseClass {
...
```

- If a library you use depends on an experimental feature, you are allowed to use the exposed API surface without enabling the experimental feature. So you have to enable the experimental feature yourself if you want to use them.

- Enabling features in a library / mod (both global and class specific) will automaticly add a tag to the metadata, in a separate file, called `experimental.json`. This file is not present if there is no experimental features used, and a jar depending on another library (JiJ) inherits the `experimental.json` file.

- Experimental code are not subject to deprecation policies, we do reserve the right to modify or remove any APIs in features without previous notice.
  
- Experimental features can be removed at any time in one of the following ways:
  - The writer of the RFC supersedes the experimental feature with another one, and after asking the working group for removal
  - The working group majority votes the removal of a feature in a meeting, after providing reasons for the removal

## `experimental.json` file
The experimental.json file only contains a JSON array with the identifiers like the following:
```json
[
    "xxx",
    "yyy"
]
```

This file can be used on quilt-loader to switch out a library at runtime, or do specific changes ahead of time.

## Modules
As defined in RFC 9, a module is the lowest level split of the standard level library, independant of each other.

### Changes specific to modules:

- A module in experimental state does not have any limitation, as long as it does not break any stable component **while the toggle is disabled**.

- It doesn't have to use a *normalized toggle*, as long as the justifications against using one is considered enough by the QSL / dependant working group.

- There is no minimal time for the experimental state, but all issues raised on the module has to be closed (see definition)

### Implementation

*Note: This implementation example is only for modules that uses the normalized toggles system, for non normalized toggles, use the method chosen.*

For experimental modules, the marking as experimental should be possible to do using the build.gradle file, by adding the following build parameter:
```gradle
// In the quilt build section
experimentalModule "xxx" // The feature name
```
On the gradle build side, we should be able to add another file in the built jar, named experimental, and containing the the feature name inside it.

This file will be read by the gradle plugin during imprt, and if the file is present, check if the feature is enabled, and if it isn't throw an error.

## APIs
APIs are smaller parts, that can be as low as a function or interface.
### Changes specific to APIs:
- APIs has to use the normalized toggle system.

- Experimental toggles cannot deprecate other functions until they are stabilized.

- All APIs has to be in the experimental status for at least one (1) month.

### Implementation
The implementation for a library developer is by adding an annotation to the experimental classes & functions, like the following:
```java
@ApiStatus.Experimental // Used for warnings directly in the IDE
@QuiltExperimental("xxx") // where "xxx" is the feature attached to the code
```

In the gradle plugin, as an additionnal compilation step, if it detects an experimental function is being called and the feature is not enabled, send an error explaining how to enable the feature.

# Drawbacks

While the experimental system allows to ensure that all stabilized code had enough chances to be tested, it also lengtens the process of getting a new feature directly available to everyone without being opt-in.


# Rationale and Alternatives

- This is an already used process, that allows us to be sure that system actually works in the wild
- This feature allows to easily test features before they get stabilized for modders, and getting their work field tested for library developers.


# Prior Art

This system is currently used by [rust](https://github.com/rust-lang), and is working pretty well. The only problem here is the requirement to have nightly to enable feature flags, even if the code itself is there. This is why the requirement was removed in this RFC.


# Unresolved Questions

- This RFC depends mainly on the gradle plugin system, with the compile time checking of features. Until then, this RFC could stay as unimplemented until then.
- How to do a file level feature selection? Attributes doesn't work on there.


# Expected Response

This change would be welcome, as it allows to use new features and give feedback to it before it's too late, and allows to bypass all deprecation policies
