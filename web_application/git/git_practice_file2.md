## Gitとは
    開発を行う際に使われるシステム


## 練習2の流れ
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


## やってみよう
1. 他人のアカウントのリモートリポジトリをコピーする
    - 練習1でやったことをおもいだしてみよう
1. リモートリポジトリをローカルリポジトリにコピーする
    - 練習1でやったことをおもいだしてみよう
1. ブランチの作成
    ```
    $ git branch -a
    $ git checkout -b 作成するブランチ名
    $ git branch -a
    $ git push -u origin 作成したブランチ名
    ```
1. 機能を追加
    - 練習1でやったことをおもいだしてみよう
1. リモートリポジトリに変更履歴を共有
    - 練習1でやったことをおもいだしてみよう


## 解説
```
$ git branch -a =「リモートブランチを含んだブランチの一覧を表示」

$ git checkout -b 作成するブランチ名 =「ブランチの作成」

$ git push -u origin 作成したブランチ名 =「作成したブランチをリモートに共有
```

[git 教材トップへ](/web_application/git.md) /
[戻る](/web_application/index.md) /
[トップへ](/README.md)