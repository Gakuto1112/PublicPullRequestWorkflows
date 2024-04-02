# MyAutoRequestReview
私（[Gakuto1112](https://github.com/Gakuto1112)）のレポジトリでプルリクエストがオープンされた際に自動で私にレビューが依頼される[GitHub Actions](https://github.co.jp/features/actions)のワークフローのレポジトリです。

## 使い方
任意のレポジトリで以下のワークフローファイルを追加してください。

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
    uses: Gakuto1112/MyAutoRequestReview/.github/workflows/request_review.yml@main
```

上記ファイルを追加したレポジトリにプルリクエストが提出されると、GitHub Actionsが私に向かってレビューを依頼します。

> [!NOTE]
> GitHubの仕様上、自分自身でプルリクエストを提出した場合には自分自身へのレビュー依頼は出されません。

> [!TIP]
> このレポジトリの`.github/workflows/request_review.yml`内の環境変数である、`reviewer`を任意のGitHubユーザー名に変更することで、そのユーザーにレビューを依頼することができます。レビューを依頼したいユーザーがレポジトリに書き込める権限を持っている必要があります。