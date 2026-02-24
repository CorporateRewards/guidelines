# Git Workflow

## Stable Branches
- `master` - used for production. Must stay stable at all time. Contains the production release history.
- `develop` - used for staging. Contains the development edge. 

## Development branches
All development work must be done in a dedicated branch. This 
makes it easy for multiple developers to work on a particular feature without 
disturbing the main codebase. It also means the `master` branch will never 
contain build breaking code. 

## Workflow
### Development
When you're ready to start development branch out from `master`

```
git checkout master
git pull
git checkout -b rew-1234_strong-passwords
```

```
master  *-*-*
             \
rew-1234      \-*-*-*-*
```

### Code Review
Once you have finished a feature, open a PR to merge into `develop`.

### Acceptance
The development branch is merged into `develop`, which should trigger a deployment
on our CI tool.

```
master      ----*
                 \
rew-1234          \-*-*-*-*
                           \
develop     ----------------m
```

### Production
Deployments to production are completed by creating a release branch from master and
successively merging the selected\* feature branches into the release branch. This allows features
to be 'cherry-picked' into production without having to wait for all features in `develop`
to receive Product Owner approval. 

\**The process for creating and deploying releases is and should be automated, and is based on the branch naming pattern. Therefore the current branch naming pattern should be honoured in order to allow branches to be auto-released. Any branches NOT matching the patter will have to be released manually. Details for this process are out of scope for this readme. See the team knowledge base for further details*

```
master          ---------*-------------------m-------
                   \      \                 /
release/20171231-101500    \----m-------m--*
                     \         /       /
feature/#n            \-*-*-*-*       / 
                       \       \     / 
feature/#x              \-*-*-*-\-*-* 
                         \       \   \ 
bug/#b                    \-*-*   \   \ 
                               \   \   \ 
develop     --------------------m---m---m----------
```


### Naming convention
Use the following naming convention*:
- Features : `feature/rew-1234-number-feature-title` or `rew-1234-number-feature-title`
- Bug: `bug/rew-1234-bug-title` or `rew-1234-bug-title`
- Technical: `technical/rew-1234-technical-issue-title` or `rew-1234-technical-issue-title`
- Grouped Issues: `group/rew-1234-group-title` or `rew-1234-group-title`
- Release: `release/yyyymmdd-hhmmss`
- Hotfix: `hotfix/yyyymmdd-hhmmss`

**The key feature of the branch name for automated releases is the reference to the ticket e.g. rew-1234 as this is how the automation knows which branches match up with which tickets*

### Resolving Conflicts
If GitHub warns you of conflicts in your PR, resolve the conflicts by merging your branch into `develop` on the command line. 
Do not use the GitHub tools or the command line to merge `develop` into your branch as this will create deployment dependencies. 
The process looks like this:
```bash
git checkout develop
git pull
git merge feature/rew-1234-strong-passwords
# resolve conflicts
git commit
git push
```

When you push to `develop`, GitHub will close the PR for you automatically. 

If you are unable to push to `develop` you may make a release branch from develop with your branch merged in to it with the conflicts fixed as part of the merge to the develop release branch instead. Your branch should NEVER have `develop` merged in to it if it is to be ultimately merged to `master`

### Rework
Assuming that a ticket has not yet gone out to `master`, then reworks should be performed on the original branch - do not create a new branch. When the rework is finished, create a PR back to `develop` as per the normal workflow.

If your branch has already gone out to `master` and a rework is requested, then a new ticket should be raised with the details of the change, and a new branch should be created from `master`.

### Special Workflow (Grouping Issues/Epic Branches)
From time to time it may become necessary to deploy certain features together. In these cases please ensure you follow the special workflow:

1. Create a issue for combining the features. (e.g. rew-5678 - Languages and React Group Deployment), referencing the original issues in the description using.
1. Create a branch for the issue ( e.g. `group/rew-5678-languages-and-react` )
1. Merge all the feature branches into the group branch.
1. Close the original issues.
1. When you're ready to deploy the entire group, you will be able to create a PR for the group branch to `master`
1. If rework is required on *any* of the issues in the group ensure that the work is applied to the new group branch, and the PR is back to `develop`.

