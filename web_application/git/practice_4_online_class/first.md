# Gitの練習1

## 流れ

1. リモートリポジトリをローカルリポジトリにコピーする
![git clone イメージ図](../images/clone_image.png)

1. 機能を追加
![お問い合わせフォーム画像](../images/contact_form_pic.png)

1. リモートリポジトリに変更履歴を共有
![git push イメージ図](../images/push_image.png)

## やってみよう

### ①リモートリポジトリをローカルリポジトリにコピーする

1. Cloneするディレクトリに移動(ない場合は作成)
    ```
    // ディレクトリがない場合
    $ mkdir practice
    $ cd practice

    // ディレクトリがすでにある場合
    $ cd practice
    ```
1. リポジトリパスをコピー
![repository pass の取得方法](../images/get_repository_pass.png)
1. ターミナルでコマンドを打つ
    ```
    $ git clone ［リポジトリパス(上の図の3でコピーしたもの)］
    ```
1. クローンしたディレクトリに移動
    ```
    $ cd [使うディレクトリ名]
    ```
1. VSCodeを開く
    ```
    $ code .
    ```


### ②index.phpファイルの修正(ない場合は作成)と反映
1. index.phpファイルに今日の日付を書く

    日付記入後は、「command + s」をして編集を保存。( ※Windowsは、「control + s」「です。)
    ![コード修正保存方法](../images/intro_save_code1.png)
   編集を保存すると白丸がなくなります。 
    ![コード修正保存方法](../images/intro_save_code2.png)

1. リモートリポジトリに変更履歴を反映させる準備

    +ボタンを押します！
    ![VSCode上でのadd方法](../images/add.png)
    コメントを書きます！(ex 今日の日付に修正)
    ![VSCode上でのcommit方法](../images/commit_msg.png)
    修正の反映の準備をします！
    ![VSCode上でのcommit方法](../images/commit.png)    

1. リモートリポジトリに変更履歴を反映させる 

    「command + shift + p」をクリックし、「push」と検索！「Git: Push」をクリックします！
    ![VSCode上でのpush方法](../images/push.png)    