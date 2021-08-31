# Gitの練習2

## 流れ

1. リモートリポジトリをローカルリポジトリにコピーする

    ![git clone イメージ図](../images/clone_image.png)

1. ブランチの作成(コードを修正するためにコピーを作成)

    ![branch イメージ図](../images/make_branch.png)

1. コードを追加または修正

    ![機能の修正or作成](../images/create_or_edit_functions.png)

1. プルリクエストの作成(コードレビューしてもらう準備)

    ![プルリクの作成 イメージ図](../images/create_pullrequest.png)

1. コードレビューとmergeをしてもらう

    ![merge イメージ図](../images/merge.png)

1. pullする(リモートの状態をローカルに反映する)

    ![pull イメージ図](../images/pull.png)

## やってみよう

### ①リモートリポジトリをローカルリポジトリにコピーする
    git clone してください

### ②ブランチの作成(コードを修正するためにコピーを作成)


### ③index.phpファイルの修正(ない場合は作成)

    index.phpファイルに今日の日付を書く

### ④プルリクエストの作成(コードレビューしてもらう準備)
    1. addする
    2. commitする
    3. プルリクエストの作成

※3のプルリクエストの作成は次の手順でおこないます！

    1. command + shift + p をクリック
    2. 「GitHub Pull Requests: Create Pull Request」をクリック

![vscodeプルリクエスト作成 イメージ図](../images/create_pullrequest_vscode.png)

![vscodeプルリクエスト作成 イメージ図](../images/create_pullrequest_vscode2.png)

### ⑤コードレビューとmergeをしてもらう

![GitHub merge pullrequest イメージ図](../images/github_merge_pullrequest.png)

### ⑥pullする(リモートの状態をローカルに反映する)

    1. command + shift + p をクリック
    2. 「Git: Pull」をクリック

![pull イメージ図](../images/pull_vscode.png)

### ⑥git log(変更が反映されているか確認！)

    1. command + shift + p をクリック
    2. 「Git: view history (git log)」をクリック