## データベースの作成方法

MySQLをインストールしたら、データベースの作成を行います。
まずはMySQLへ接続しましょう。

```bash
mysql -u root -p
```

上記コマンドを実行すると、パスワード入力が求められますので、`MySQLのインストール手順`の時に決めたパスワードを
入力しましょう。ログインに成功すると、SQLの入力モードになります。

```bash
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 7
Server version: 5.7.25 Homebrew

Copyright (c) 2000, 2019, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

この状態となったら、まずデータベースを作成します。以下のコマンドを実行します。

```bash
mysql> create database sample;
```

成功するとOKと表示されます。次に`作成したデータベースを使う`、という宣言をします。以下のコマンドを実行します。

```bash
mysql> use sample;
```

次に、データベースにアクセスするための`ユーザー名とパスワード`を作成します。
以下がその構文となります。

```bash
create user 【ユーザー名】 IDENTIFIED BY 'パスワード';
```

ここでは、ユーザー名をsamplelaravel、パスワードをsamplequestにしましょう。
では実際に以下のコマンドを実行します。

```bash
mysql> create user samplelaravel IDENTIFIED BY 'samplequest';
```

次に作成したユーザーに対して、データベースにどんなことを行えるかを決定する、`権限を付与する`ということを行います。先程作成したユーザー名「samplelaravel」に、データベースsampleに対してすべての事ができる権限を付与します。

```bash
mysql> grant all privileges on sample.* to 'samplelaravel';
```

上記で完了です。改めて以下のように作成しました

 - データベース名：sample
 - ユーザー名：samplelaravel
 - パスワード：samplequest

書籍管理アプリの開発でこのデータベースを利用する場合は、覚えておきましょう。

mysqlの入力モードを終了するには、`exit;` で抜けましょう。

以上です、お疲れさまでした。


[laravelチュートリアルに戻る](/web_application/laravel_tutorial.md) /
[トップへ](/README.md)