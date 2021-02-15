Yay! Thanks for helping us build OpenDEX! Any contributions are welcome no matter how big or small. From typos and bug fixes to entire modules...

## Contribution Guidelines

If you are new to contributing to open sources projects on GitHub, check out [this how-to](https://egghead.io/courses/how-to-contribute-to-an-open-source-project-on-github/).

### Feature Branches & Pull Requests

We use [feature branches](https://www.atlassian.com/git/tutorials/comparing-workflows/feature-branch-workflow) for devlopment. Each branch and pull request should focus on a particular feature or issue. Ensure that new branches are created from the latest code in `main`. A branch must be in a working and stable state before it can be merged into `main`.

Pull requests will be reviewed and changes may be requested. If changes are required, make new commits directly to the feature branch. If there have been conflicting commits to `main` since a feature branch was created, rebase the branch onto main using `git rebase`.

For pull requests of small scope, you may squash multiple commits into a single commit or a maintainer will squash them before merging. For more complex pull requests that touch many parts of the code or which contain commits from multiple contributors, please collapse commits by author and affected files or components of the project. This can be performed through an interactive rebase with `git rebase -i`. Use `git commit --amend` when fixing minor typos or bugs with the most recent commit. These practices help maintain a clean and coherent commit history while preserving contribution authorship.

### Linting & Testing

New test cases are appreciated for any pull requests that changes or adds new functionality. Although the current test suites are in early stages, thorough testing and code coverage is an important long term goal.

All code is linted with [tslint](https://github.com/palantir/tslint) using a slightly modified [tslint adaptation](https://github.com/progre/tslint-config-airbnb) of [Airbnb's javascript style guide](https://github.com/airbnb/javascript). Ensure that your contributions pass all linting rules by running `npm run lint` or using a code editor with a tslint plugin. Per-line or per-file exceptions to linting rules are allowable in certain cases.

### Commenting & Documentation

Make your code legible by using descriptive variable, function, and class names. For blocks of codes whose function is not readily apparent, add comments explaining what they do. If a pull request changes the interface to `opendexd` or introduces new functionality, update the README to describe these changes.

`opendexd` uses [TypeDoc](http://typedoc.org/guides/doccomments/) and any comments documenting specific classes, methods, properties should follow this convention.

Commit messages should be concise and descriptive. For larger or more complex commits, add details of what has changed in the commit description.

If you see something missing or want to develop another cool feature or bug fix which doesn't have an issue yet, open one and we'll take it from there. We don't recommend working on things without a GitHub issue.