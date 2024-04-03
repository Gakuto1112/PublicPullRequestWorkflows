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

## ワークフローの仕組み
プルリクエストがオープンされると`request_review_trigger.yml`が、レビュワーがレビュイーに対して変更を要求すると`comment_when_changes_requested_trigger.yml`が実行されます。
これらのワークフローはプルリクエストのイベントデータをアーティファクトとしてアップロードします。
これらのアーティファクトの有効期限は1日です。

通常、GitHub Actionsによるプルリクエストの操作にはプルリクエストに対する書き込み権限が必要です。
プルリクエストに書き込み権限を与えるにはワークフローのトリガーイベントを`pull_request_target`にする必要がありますが、これを使用すると悪意のあるレビュイーによってレポジトリシークレットが盗まれる可能性があります。
このレポジトリのワークフローでは、`pull_request_target`の代わりに`pull_request`と`workflow_run`を併用しているため、パブリックレポジトリでも安全にワークフローを実行できます。
詳しくは以下の記事をご覧ください。

[Keeping your GitHub Actions and workflows secure Part 1: Preventing pwn requests](https://securitylab.github.com/research/github-actions-preventing-pwn-requests/)

上記2つのワークフローの実行が完了すると、それぞれ`request_review.yml`と`comment_when_changes_requested.yml`が実行されます。
先に実行したワークフローのアーティファクトのデータを基にプルリクエストを操作します。

これらのワークフローにはプルリクエストへの書き込み権限が与えられていますが、この2つのワークフローは常にベースレポジトリのベースブランチのワークフローファイルから実行されるため、悪意のレビュイーにレポジトリシークレットが盗まれることがありません。