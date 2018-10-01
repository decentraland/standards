# Decentraland's git style guide

**Document Status**: *Draft*.  
**Maintainer**: Agustin Mendez &lt;[agus@decentraland.org](mailto:agus@decentraland.org)&gt;.

# Introduction

We agreed on normalizing commit messages to master branches to avoid things like `Merge pull request #15 from client/menduz-patch-10` in persuit of more semantic messages like `fix: commit style guide, closes #15`. That is particularly helpful in repositories with several contributors and fosters professionalism in open source repositories.

# Commit messages

In master branches and pull requests, we do [semantic commits](https://seesparkbox.com/foundry/semantic_commit_messages).

```
feat: add hat wobble
^--^  ^------------^
|     |
|     +-> Summary in present tense.
|
+-------> Type: chore, docs, feat, fix, refactor, style, or test.
```

Examples:

```yaml
chore: add Oyster build script
```
```yaml
docs: explain hat wobble
```
```yaml
feat: add beta sequence, implements #332
```
```yaml
fix: remove broken confirmation message, closes #123
```
```yaml
refactor: share logic between 4d3d3d3 and flarhgunnstow
```
```yaml
style: convert tabs to spaces
```
```yaml
test: ensure Tayne retains clothing
```

## Allowed `<type>` values:
   * `feat` new feature
   * `fix` bug fix
   * `docs` changes to the documentation
   * `style` formatting, linting, etc; no production code change
   * `refactor` refactoring production code, eg. renaming a variable
   * `test` adding missing tests, refactoring tests; no production code change
   * `chore` updating build tasks etc; no production code change

# Merge pull requests

We do squash and merge for pull requests. The squashed commit message must follow the [semantic commits](https://seesparkbox.com/foundry/semantic_commit_messages) rules.

Since we are squashing all the commits, inside a pull request's commits it is RECOMMENDED to follow this same convention. It is a team/contributor decision, as long as the title of the PR (and therefore the commit message to be merged) follows this reference.

# Referencing issues or JIRA tasks

Please refer to the issue or JIRA ticket so we can track the progress like this: 
```yaml
fix: some bug, closes #14
```
or 
```yaml
fix: some bug, fixes [CLIENT-1]
```

> Note: JIRA tickets require [square brackets]

# Merge commits

Avoid `Merge branch 'a' into 'master'` commit messages. Rebase when possible.

# Security

Sign all your commits with GPG. If you have a physical key, use that key. Otherwise set up a GPG key in your computer.
In the future we will add a CI process to verify commit signatures.

- https://github.com/blog/2144-gpg-signature-verification
- https://help.github.com/articles/generating-a-new-gpg-key/
- https://help.github.com/articles/signing-commits-using-gpg/
