Language: 　**English**　|　[日本語](./README_jp.md)

# PublicPullRequestWorkflows
These are [GitHub Actions](https://github.com/features/actions) workflows related to pull requests in public repositories.
You can use these workflows just by adding some workflow files to `./github/workflows` in any repository.

The following workflows are available.

- Request a review to the specific GitHub user when a pull request is opened.
- Send a comment to the reviewee that encourage them to re-request a review to the reviewer when the reviewer requested the reviewee to make some changes.

These workflows are designed to prevent malicious reviewees from abusing pull requests, and can be safely used in public repositories where any GitHub users can be reviewees.

## 使い方
### Request review
プルリクエストがオープンになった際に、予め設定したGitHubユーザーにレビューの依頼を行うワークフローです。

`src/`内にある以下のファイルをレポジトリの`.github/workflows/`に追加してください。

- request_review.yml
- request_review_trigger.yml

上記の各ファイル内の`reviewer: <github_username>`に任意のGitHubユーザー名に書き換えます。

> [!NOTE]
> - 複数ユーザーへのレビュー依頼には対応していません。
> - レビューを依頼するユーザーが対象のレポジトリへの書き込み権限を有している必要があります。

レポジトリでのデフォルト設定では、初回のコントリビューターのプルリクエストに対しては、レポジトリ管理者の承認がないとワークフローが実行されない仕様になっています。
この設定は レポジトリ設定 → 「Actions」 → 「General」 → 「Fork pull request workflows from outside collaborators」から変更できます。

### Comment when changes required
レビュワーがプルリクエストに対して変更を要求した場合にレビュイーに対して、変更が完了した際に再度レビューを依頼することを促すコメントを送信するワークフローです。

実際にレビュワーがレビュイーに対して変更を要求すると以下のようなコメントがプルリクエストに投稿されます。

> @<reviewee_name><br>
> <reviewer_name> requested you to make some changes.
> Once you make changes, please re-request <reviewer_name> to review by clicking "re-request review" button 🔄 (located in the reviewers list on the right side of this page).

`src/`内にある以下のファイルをレポジトリの`.github/workflows/`に追加してください。

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