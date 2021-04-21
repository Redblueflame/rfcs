## Notes
Should use custom attributes

Has to be checked in compiler stage

Has to be enabled in both top of files and in project settings

## Examples
Networking api could be a useful example
Major refactor of the ressource loader (Needs to disable a standard & active feature to work, has to take that into account.)

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

## General system

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

Once every TODO and blocking issue is closed, the feature can be added to the next related working group meeting, where the team will vote on the stabilization of the feature. If the stabilization is refused, the team has to provide reasoning behinf the choice

In order to use experimental code, you have to switch a *toggle*, if not, the compiler should return an error including a small explaination into how to enable the *toggle*.



## Modules
As defined in RFC 9, a module is the lowest level split of the standard level library, independant of each other.

### Limitations:

- A module in experimental state does not have any limitation, as long as it does not break any stable component **while the toggle is disabled**.

- It doesn't have to use a *normalized toggle*, as long as the justifications against using one is considered enough by the QSL / dependant working group.

- There is no minimal time for the experimental state, but all issues raised on the module has to be closed (see definition)

### Process:
On its first implementation, the module has to contain a configuration attribute in the build.gradle file marking itself as experimental.



# Drawbacks

Why should we not do this?


# Rationale and Alternatives

- Why is this the best possible design?
- What other designs are possible and why should we choose this one instead?
- What other designs have benefits over this one? Why should we choose an
  alternative instead?
- What is the impact of not doing this?


# Prior Art

If this has been done before by some other project, explain it here. This could
be positive or negative. Discuss what worked well for them and what didn't.

There may not always be prior art, and that's fine.


# Unresolved Questions

- What should be resolved before this RFC gets merged?
- What should be resolved while implementing this RFC?
- What unresolved questions do you consider out of scope for this RFC, that
  could be addressed in the future?


# Expected Response

How do might the wider community respond to this change? Who will be affected
by it and how? Who has advocated for this change? Who has advocated against it?
