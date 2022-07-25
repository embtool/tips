# Tips to Improve Development

# Table of Contents

- [Trunk Based Development](#trunk-based-development)
- [Do NOT use Feature Branches](#do-not-use-feature-branches)
- [Do NOT use Merge Requests](#do-not-use-merge-requests)
- [Version Control the Input of Code Generation Tools](#version-control-the-input-of-code-generation-tools)

# Trunk Based Development

_Trunk Based Development_, also known as _Continuous Integration_,
is a development policy with minimal branching and frequent pushes.
The developer uses the local main branch to code and test.
Then, after a small milestone, the developer pushes the changes
directly to the repository's main branch.

The smaller the time between pushes the better: a good starting point is
at least once a day.
By doing this, any developer is one `git pull` away from seeing
everybody else's changes.
The merge conflicts are always much smaller, simpler and fresh.

Using _Trunk Based Development_ is the best way to:

- Boost productivity
- Avoid merge pain
- Avoid dead branches

_Trunk Based Development_ has a simple and consistent workflow:

```code
Update local branch (possible merge conflicts)
$ git pull

Coding ...
Testing ...

Create a commit
$ git add --all
$ git commit -m "Feature X Create struct initialization"

Update local branch (possible merge conflicts)
$ git pull

Push changes
$ git push
```

## What if someone deletes (or pushes) something they shouldn't?

We all understand how hard it is to give new people write permission in
the repository. However there is a nice middle ground where it is
possible to write (can push) but not delete (cannot force-push):

To remove the possibility of deleting something, you can protect the
main branch, to avoid forced pushes that can delete other people's
commits.

Since you cannot force-push a bad commit anymore,
if something was pushed and it shouldn't, you can `git revert COMMIT`
to undo the commit.

If that something MUST NOT even be in the history (sensitive
information, such as access keys) then you will need to force-push
that commit away.

# Do NOT use Feature Branches

_Features Branches_, or _Long Lived Branches_,
is a development policy that uses branches to isolate changes among
the features under development.
Every feature gets its own branch that starts at a "stable point" and
will not have to deal with changes from other features.
Only until it is time to merge them.

The objective of the _Feature Branches_ is to isolate the features under
development (\*).
However it also isolates the developers, that cannot see each other's
changes, and the tests they execute.

(\*) We actually do not need to "isolate features under development",
keep reading.

When it is time to merge, then comes the great _Merge Pain_:
a massive merge conflict, giant differences in several files.

Now add the fact that at least a few weekends have passed for most of
the people involved in the conflicting changes.
All of that is not fresh in their minds anymore, their heads are
dealing with solving other problems.

To my knowledge, there are three ways to avoid the _Merge Pain_:

1. Developer procrastinate merging.
   - Sooner or later it will have to be merged or the branch forgotten
     and will die.
   - Code quality suffers because of long and tedious work of merging.
2. Developer rush the implementation, to be the first to merge and get
   the cleanest merge possible.
   - Soon everybody is rushing.
   - The code quality suffers because of the rush, allowing for more
     mistakes to pass.
3. Use _Trunk Based Development_.
   - Do this one!

Beyond that, several branches only add to the developer's
_Cognitive Burden_.
Not only the developer needs to know what to work on now,
but also has to figure out what is the correct branch name, what commit
to use as base for the branch, to give the correct push command, etc.

Actually we do not need to isolate new features from each other.
What we need is to isolate new features from the production code.
That is what _Feature Toggles_ are designed for.

And, at this point, we need to consider that the code that will go to
production is the code **after** the merge, therefore we also need to
avoid errors in the merge resolution:

- Use [Trunk Based Development](#trunk-based-development).
  - Merge frequently, minimal conflicts, and changes fresh.
  - No procrastination when merging.
  - No rush when coding.
- Use [Feature Toggles](#feature-toggles) to isolate features under
  development from the production code.
  - Development code is isolated from production code.

## What Are Feature Branches Good For?

If you need all features A, B, C, etc, you have to code them and
merge them.
In this case _Feature Branches_ are terrible, because the longer you
wait the larger will be the effort in the merge.

However, one thing that _Feature Branches_ are good, is when you are
researching how to better implement ONE feature: using implementation
X, Y, or Z and they cannot exist at the same time.

Therefore _Feature Branches_ are good **in this case** because you can
start all implementations from the same point and compare them, so that
a decision will be made and all but one of X, Y, or Z will be dropped.

# Do NOT use Merge Requests

_Merge Requests_, or _Pull Requests_, is a development policy used to
gate-keep changes from entering the repository.
The code becomes part of the repository only when the _Merge Request_
is approved.

I think we can go straight to the point:

- Do not block the developers from merging changes.
- Do not block the merge with a code review.

~~It is NOT about No Code Review!~~

It is about **Review After Merge**.

To better explain why _Merge Requests_ are bad in general, let's start
talking about what they are good for.

## What Are Merge Requests Good For?

_Merge Requests_ make sense for open-source projects to review the
changes of people who don't have write access to the repository.

Anyone can fork an open-source project, make changes and create
a _Merge Request_.

Since we need to take care of the project, we cannot just give write
permission to random people.
So the changes need to wait for the review before they can be accepted
into the project.

_Merge Requests_ are NOT meant for reviewing changes.
They are a tool that can, but should not be used for that.

_Merge Requests_ are meant for reviewing changes from random people.

## Why Are Merge Requests Bad in General?

_Merge Requests_ are slow and inefficient.

It is boring to wait for a review, it is annoying to keep
asking for a review to be done, it is annoying to be constantly asked
to do a review.

While waiting for the review, the developer cannot test his changes
with other people's changes, who are also blocked waiting for their
own reviews.

We often hear that we need code reviews to share knowledge and
experience to elevate code quality. We agree with that.

However a code review is not meant to block the merge.
The merge serves the purpose of merging changes, it is not meant for
reviewing changes.

Assume the developers create good code in the first place.
If they are not doing it, they should be trained to do that through
reviews.
Gate-keeping them from merging is not the way the solve this.

Changes can be merged, then reviewed and fixed, or even reverted.

A _Development Process_ should be put in place to decide when and how
to review.

# Version Control the Input of Code Generation Tools

The inputs for _Code Generation Tools_ are also code and must be
available in the repository.
That is also true for the scripting and documentation of those tools.

It is worth pointing that textual inputs are preferred over binary
inputs, because they can be diffed and are easier to review, and
automated generation is also preferred over manual copy-paste.

The merge conflicts are specially painful when they involve code
generated by tools.
In general, these are large chunks of code with tedious reviews and
large diffs.
It probably is much easier to diff, review and update the input instead
of the code generated.

- Prefer automated or scripted tools instead of manual tools.
- Prefer tools with textual inputs, which can be diffed and reviewed.
  - CSV and JSON are great!
  - See your diff, apply on the updated version and generate the new
    code.
- Add fields for documentation, which can stay only in the input file
  or maybe be included in the generated code.

# TODO

- Everything You Need to Build in One Repository
- Feature Toggles
- Merge Instead of Rebase
  - Only one merge conflict
  - History shows the actual development steps
- Track changes with _Issues_ in a project management software or
  bug tracker, not with branches.
