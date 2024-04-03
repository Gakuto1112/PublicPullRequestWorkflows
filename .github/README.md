Language: 　**English**　|　[日本語](./README_jp.md)

# PublicPullRequestWorkflows
These are [GitHub Actions](https://github.com/features/actions) workflows related to pull requests in public repositories.
You can use these workflows just by adding some workflow files to `./github/workflows` in any repository.

The following workflows are available.

- Request a review to the specific GitHub user when a pull request is opened.
- Send a comment to a reviewee that encourage them to re-request a review to a reviewer when the reviewer requested the reviewee to make some changes.

These workflows are designed to prevent malicious reviewees from abusing pull requests, and can be safely used in public repositories where any GitHub users can be reviewees.

## How to use
### Request review
This workflow will request a review to the specific GitHub user when a pull request is opened.

Please add the following files in `src/` to `./github/workflows` to the target repository.

- request_review.yml
- request_review_trigger.yml

Please replace `reviewer: <github_username>` with any GitHub username in each of the above files.

> [!NOTE]
> - Requesting reviews to multiple users are not supported.
> - The user who will be requested a review must have write access to the target repository.

With default settings in the repository, deWorkflows that are triggered by a pull request by first time contributors must be approved by repository maintainers.
This setting can be changed from repository settings → "Actions" →　"General" → "Fork pull request workflows from outside collaborators".

### Comment when changes required
This workflow will send a comment to a reviewee that encourage them to re-request a review to a reviewer when the reviewer requested the reviewee to make some changes.

The following comment will be sent to the target pull request when a reviewer requests a reviewee to make some changes.

> @<reviewee_name><br>
> <reviewer_name> requested you to make some changes.
> Once you make changes, please re-request <reviewer_name> to review by clicking "re-request review" button 🔄 (located in the reviewers list on the right side of this page).

Please add the following files in `src/` to `./github/workflows` to the target repository.

- comment_when_changes_requested.yml
- comment_when_changes_requested_trigger.yml

## Workflow mechanisms
`request_review_trigger.yml` will be triggered when a pull request is opened, and `comment_when_changes_requested_trigger.yml` will be triggered when a reviewer requests a reviewee to make some changes.
These workflows upload event data of pull requests as artifacts.
These artifacts will expire in a day.

Usually, manipulating pull request with GitHub Actions requires write permission to pull requests.
The workflow trigger event must be set to `pull_request_target` to give write permission to pull requests.
However, malicious reviewees may steal your repository secrets by falsifying workflow files if `pull_request_target` are used as a workflow trigger event.
Workflows in this repository use `pull_request` and `workflow_run` together instead of `pull_request_target`, so these workflows can be safely executed even in public repositories.
Please see the following article for more information.

[Keeping your GitHub Actions and workflows secure Part 1: Preventing pwn requests](https://securitylab.github.com/research/github-actions-preventing-pwn-requests/)

`request_review.yml` and `comment_when_changes_requested.yml` will be respectively execute after finishing executing the above two workflows.
They will manipulate pull requests based on data from artifacts by workflows that previously executed.

These two workflows are given write permission to pull requests.
However, they are always executed from the base branch in the base repository, so malicious reviewees cannot steal your repository secrets.