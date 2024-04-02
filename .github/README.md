# MyAutoRequestReview
プルリクエストがオープンされた際に自動で設定したGitHubユーザーにレビューが依頼される[GitHub Actions](https://github.co.jp/features/actions)のワークフローのレポジトリです。

## 使い方
任意のレポジトリで以下のワークフローファイルを追加してください。`<github_username>`にはレビューを依頼したいGitHubユーザーの名前を入れてください。なお、レビューを依頼したい人は該当レポジトリへの書き込み権限を持つ必要があります。

`.github/workflows/request_review.yml`

```yml
name: Request review

on:
  pull_request_target:
    types:
      - opened
      - ready_for_review

jobs:
  call_workflow:
    name: Call workflow
    permissions:
      pull-requests: write
    uses: Gakuto1112/MyAutoRequestReview/.github/workflows/request_review_core.yml@main
    with:
      reviewer: <github_username>
```

上記ファイルを追加したレポジトリにプルリクエストが提出されると、GitHub Actionsが対象のGitHubユーザーに向かってレビューを依頼します。

> [!NOTE]
GitHubの仕様上、自分自身でプルリクエストを提出した場合には自分自身へのレビュー依頼は出されません。