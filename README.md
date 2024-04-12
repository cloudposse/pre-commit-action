cloudposse/github-action-pre-commit
=================

A GitHub action to run [pre-commit](https://pre-commit.com)

__NOTE:__ This is a fork of [pre-commit/action](https://github.com/pre-commit/action) to add additional features.

Permissions required: `repo` and `workflow`

### using this action

To use this action, make a file `.github/workflows/pre-commit.yml`.  Here's a
template to get started:

```yaml
name: pre-commit

on:
  pull_request:
  push:
    branches: [master]

jobs:
  pre-commit:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    - uses: actions/setup-python@v5
    - uses: cloudposse/github-action-pre-commit@v4.0.0
```

This does a few things:

- clones the code
- installs python
- sets up the `pre-commit` cache

### using this action with custom invocations

By default, this action runs all the hooks against all the files.  `extra_args`
lets users specify a single hook id and/or options to pass to `pre-commit run`.

Here's a sample step configuration that only runs the `flake8` hook against all
the files (use the template above except for the `pre-commit` action):

```yaml
    - uses: cloudposse/github-action-pre-commit@v4.0.0
      with:
        extra_args: flake8 --all-files
```

### using this action in private repositories

this action also provides an additional behaviour when used in private
repositories.  when configured with a github token, the action will push back
fixes to the pull request branch.

using the template above, you'll make two replacements for individual actions:

first is the checkout step, which needs to use unlimited fetch depth for
pushing

```yaml
    - uses: actions/checkout@v2
      with:
        fetch-depth: 0
```

next is passing the token to the pre-commit action

```yaml
    - uses: cloudposse/github-action-pre-commit@v4.0.0
      with:
        token: ${{ secrets.GITHUB_TOKEN }}
        git_user_name: pre-commit
        git_user_email: pre-commit@example.com
        git_commit_message: "pre-commit fixes"
```

note that `secrets.GITHUB_TOKEN` is automatically provisioned and will not
require any special configuration.

while you could _technically_ configure this for a public repository (using a
personal access token), I can't think of a way to do this safely without
exposing a privileged token to pull requests -- if you have any ideas, please
leave an issue!
