# Gitとは

    開発を行う際に使われるシステム

## 練習1の流れ

1. 他人のアカウントのリモートリポジトリをコピーする
![git fork イメージ図](images/fork_image.png)

1. リモートリポジトリをローカルリポジトリにコピーする
![git clone イメージ図](images/clone_image.png)

1. 機能を追加

1. リモートリポジトリに変更履歴を共有
![git push イメージ図](images/push_image.png)

## やってみよう

1. 他人のアカウントのリモートリポジトリをコピーする


1.コピーしたいリポジトリがあるGithubアカウントでforkをクリック
2.コピー先のアカウントを選択する


[ここからコピーするアカウントへ](https://github.com/KatsuyaTanaka95/app_contact/commit/ee4a5ee0b104b06a82434d874fc9c00b1b008111)
![git push イメージ図](images/fork_github.png)

1. リモートリポジトリをローカルリポジトリにコピーする

    1. リポジトリパスをコピー
    2. Cloneするディレクトリを作成
    3. Cloneするディレクトリに移動
    4. ターミナルでコマンドを打つ

    ```bash
    $ git clone ［リポジトリパス］
    ```

    5. 使用するディレクトリに移動

    ```
    $ cd [使うディレクトリ名]
    ```
    
    6. VSCodeで開く

    ![repository pass の取得方法](images/get_repository_pass.png)

1. 機能を追加
    ```
    何かしらの機能を追加
    ```
1. リモートリポジトリに変更履歴を共有
    ```
    $ git add .
    $ git commit  -m “コミットメッセージ”
    $ git push origin
    ```
