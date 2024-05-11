言語: 　[English](./README.md)　|　**日本語**

# PublicPullRequestWorkflows
パブリックレポジトリでのプルリクエストに関する、再利用可能な[GitHub Actions](https://github.co.jp/features/actions)ワークフローです。
任意のレポジトリの`.github/workflows/`内にワークフローのファイルを追加するだけで利用できます。

このレポジトリには以下のワークフローが利用可能です。

- プルリクエストがオープンになった際に、予め設定したGitHubユーザーにレビューの依頼を行う。
- レビュワーがプルリクエストに対して変更を要求した場合にレビュイーに対して、変更が完了した際に再度レビューを依頼することを促すコメントを送信する。

これらのワークフローは、悪意のあるレビュイーがプルリクエストを悪用できないような設計になっており、不特定多数のユーザーがレビュイーになり得るパブリックレポジトリでも安全に利用できます。

## 使い方
### Request review
プルリクエストがオープンになった際に、予め設定したGitHubユーザーにレビューの依頼を行うワークフローです。

`workflow_templates/`内にある以下のファイルをレポジトリの`.github/workflows/`に追加してください。

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

`workflow_templates/`内にある以下のファイルをレポジトリの`.github/workflows/`に追加してください。

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