## Gitとは
    開発を行う際に使われるシステム

## 練習3の流れ
1. 他人のアカウントのリモートリポジトリをコピーする
![git fork イメージ図](images/fork_image.png)

1. リモートリポジトリをローカルリポジトリにコピーする
![git clone イメージ図](images/clone_image.png)

1. ブランチの作成
![branch イメージ図](images/branch_image.png)

1. 機能を追加
![お問い合わせフォーム画像](images/contact_form_pic.png)

1. リモートリポジトリに変更履歴を共有
![git push イメージ図](images/development_with_branch_image.png)

1. プルリクエストの作成
1. マージ(追加機能を反映)
![プルリク・マージ イメージ図](images/pullrequest_merge_image.png)


## やってみよう
1. 他人のアカウントのリモートリポジトリをコピーする
    - 練習1でやったことをおもいだしてみよう
1. リモートリポジトリをローカルリポジトリにコピーする
    - 練習1でやったことをおもいだしてみよう
1. ブランチの作成
    - 練習2でやったことをおもいだしてみよう
1. 機能を追加
    - 練習1でやったことをおもいだしてみよう
1. リモートリポジトリに変更履歴を共有
    - 練習1でやったことをおもいだしてみよう
1. プルリクエストの作成

    [参考URL](https://ics.media/entry/14449/)
1. マージ
    1. Github を開き、Pull requests をクリック
    ![マージ手順1](images/check_pullrequest.png)
    1. Pull requests されたものをクリック
    ![マージ手順2](images/check_pullrequest_details.png)
    1. コメント、修正依頼を書く
        ```
        1.右上の Files changed を選択。
        2.変更箇所の左にある + ボタンを選択肢。
        3.コメントを書く。
        ```
       ![マージ手順3](images/write_comment_pullrequest.png)
    1. コメント、修正依頼を送信
        ```
        1.Pending と表示される
        2.右上の Finish your reviwes をクリック
        3.3つの選択肢から正しいものを選び、submit reviwe をクリック
        ```
        ![マージ手順4](images/send_comments_pullrequest.png)
    1. マージする
        ![マージ手順5](images/send_comments_pullrequest.png)
        ```
        1.緑の Merge pull request ボタンをクリック
        ```
        ![マージ手順5-1](images/merge_pull_request.png)
        ```
        2.緑の Confirm merge をクリック
        ```
        ![マージ手順5-1-1](images/confirm_merge.png)
        ![マージ手順5-1-2](images/confirm_merge2.png)
