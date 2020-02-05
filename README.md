# git-auto-commit-action

This GitHub Action automatically commits files which have been changed during a Workflow run and pushes the Commit back to GitHub.
The Committer is "GitHub Actions <actions@github.com>" and the Author of the Commit is "Your GitHub Username <github_username@users.noreply.github.com>.

If no changes are detected, the Action does nothing.

This Action has been inspired and adapted from the [auto-commit](https://github.com/cds-snc/github-actions/tree/master/auto-commit
)-Action of the Canadian Digital Service and this [commit](https://github.com/elstudio/actions-js-build/blob/41d604d6e73d632e22eac40df8cc69b5added04b/commit/entrypoint.sh)-Action by Eric Johnson.

*This Action currently can't be used in conjunction with pull requests of forks. See [issue #25](https://github.com/stefanzweifel/git-auto-commit-action/issues/25) for more information.*

## Usage

**Note:** This Action requires that you use `action/checkout@v2` or above to checkout your repository.

Add the following step at the end of your job.

```yaml
- uses: stefanzweifel/git-auto-commit-action@v2.5.0
  with:
    commit_message: Apply automatic changes
    branch: ${{ github.head_ref }}

    # Optional git params
    commit_options: '--no-verify --signoff'

    # Optional glob pattern of files which should be added to the commit
    file_pattern: src/\*.js

    # Optional local file path to the repository
    repository: .

    # Optional commit user and author settings
    commit_user_name: My GitHub Actions Bot
    commit_user_email: my-github-actions-bot@example.org
    commit_author: Author <actions@gitub.com>
```

The Action will only commit files back, if changes are available. The resulting commit **will not trigger** another GitHub Actions Workflow run!

It is recommended to use this Action in Workflows which listen to the `pull_request` event. If you want to use the Action on other events, you have to hardcode the value for `branch` as `github.head_ref` is only available in Pull Requests.

## Example Usage

This Action will only work, if the job in your Workflow changes project files.
The most common use case for this, is when you're running a Linter or Code-Style fixer on GitHub Actions.

In this example I'm running `php-cs-fixer` in a PHP project.

```yaml
name: php-cs-fixer

on: pull_request

jobs:
  php-cs-fixer:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
      with:
        ref: ${{ github.head_ref }}

    - name: Run php-cs-fixer
      uses: docker://oskarstark/php-cs-fixer-ga

    - uses: stefanzweifel/git-auto-commit-action@v2.5.0
      with:
        commit_message: Apply php-cs-fixer changes
        branch: ${{ github.head_ref }}
```

### Inputs

Checkout [`action.yml`](https://github.com/stefanzweifel/git-auto-commit-action/blob/master/action.yml) for a full list of supported inputs.

## Troubleshooting

- If your Workflow can't push the commit to the repository because of authentication issues, please update your Workflow configuration and usage of [`ations/checkout`](https://github.com/actions/checkout#usage). (Updating the `token` value with a Personal Access Token should fix your issues)

## Known Issues

- GitHub currently prohibits Actions like this to push changes from a fork to the upstream repository. See [issue #25](https://github.com/stefanzweifel/git-auto-commit-action/issues/25) for more information.

## Versioning

We use [SemVer](http://semver.org/) for versioning. For the versions available, see the [tags on this repository](https://github.com/stefanzweifel/git-auto-commit-action/tags).

## License

This project is licensed under the MIT License - see the [LICENSE](https://github.com/stefanzweifel/git-auto-commit-action/blob/master/LICENSE) file for details.
