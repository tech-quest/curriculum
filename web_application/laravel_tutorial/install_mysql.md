## MySQLのインストール手順

システムにデータを保存するには、データベースが必要です。
ここでは、無料で利用できかつ高機能である、`MySQL`を導入します。

MySQLについては、以下のサイトをご覧ください。

https://www.kagoya.jp/howto/webhomepage/mysql/


#### **0. 事前準備**

実際にインストールをする前に、事前準備をします。
すでに以下がインストール済みの場合は割愛してください。

    ・ MySQL
    ・ Homebrew

まずはMySQLがインストールされていないことを確認するために、ターミナルを起動してから、以下のコマンドを実行します。

```bash
mysql --verion
```

インストールがされていないことを確認しましょう。以下の表示が出力されます。

```bash
-bash: mysql: command not found
```

上記が確認できたら、MySQLがインストールされていない確認になります。
MySQLのインストールは、`brew`というコマンドで実行しますが、Homebrewをインストールしないと動作しません。
Homebrewがインストールされているか確認しましょう。

```bash
brew -v
```

```bash
bash: brew: command not found
```

上記コマンドでcommand not foundなどが表示されたら、Homebrewをインストールしましょう。

```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

以下のコマンドを実行します。これはターミナルのどこのディレクトリにいても、brewというコマンドを実行できるようにします。
「PATHを通す」と言い、設定ファイルに記載します。

```bash
echo export PATH='/usr/local/bin:$PATH' >> ~/.bash_profile
```

以下を実行して、設定ファイルを読み込みし直します。

```bash
source ~/.bash_profile
```

PATHについては、以下のサイトが参考になりますので、ご確認ください。

https://qiita.com/soarflat/items/09be6ab9cd91d366bf71


#### 1. MySQLのインストール

MySQLのインストールがされていないことを確認したら、以下の手順でインストールしましょう。

```bash
brew install mysql@5.7
```

次に以下の手順を実行します。

```bash
brew services start mysql
```

mysqlの利用が出来るように、PATHを通します。

```bash
echo 'export PATH="/usr/local/opt/mysql@5.7/bin:$PATH"' >> ~/.bashrc
```

PATHの設定を反映しましょう。
```bash
source ~/.bashrc
```

#### 2. MySQLのセキュリティ設定

次に、MySQLのセキュリティ（パスワード）を設定します。
なお、ここで設定したパスワードは、先程自端末（ローカル）にインストールしたMySQLに、毎回接続する際に必要となりますので、
必ず覚えておきましょう。

以下のコマンドを実行します。

```bash
mysql_secure_installation
```

成功すると、以下の表示となります。
最初に「VALIDATE PASSWORDプラグインを設定しますか？」と推奨されます。
`y`を入力して、Enterで進めてください。

```bash
Securing the MySQL server deployment.

Connecting to MySQL using a blank password.

VALIDATE PASSWORD PLUGIN can be used to test passwords
and improve security. It checks the strength of password
and allows the users to set only those passwords which are
secure enough. Would you like to setup VALIDATE PASSWORD plugin?

Press y|Y for Yes, any other key for No: y
```

次に以下のような画面となります。

セキュリティの強度をLOW、MEDIUM、STRONGの3段階でパスワードを選ぶことができます。

今回はLOWで進めますので、0を選択します。

```bash
There are three levels of password validation policy:

LOW    Length >= 8
MEDIUM Length >= 8, numeric, mixed case, and special characters
STRONG Length >= 8, numeric, mixed case, special characters and dictionary file

Please enter 0 = LOW, 1 = MEDIUM and 2 = STRONG: 0
```

パスワードを入力していきます。自分で決めた、`同じパスワードを二回入力`しましょう。
最後にパスワードの強度が100段階中で表示されます。
指定したパスワードでよければyを押して進めてください。

```bash
Please set the password for root here.

New password:

Re-enter new password:

Estimated strength of the password: 50
Do you wish to continue with the password provided?(Press y|Y for Yes, any other key for No) : y
```

MySQLはデフォルトで匿名ユーザの情報が入っています。いらないので消しましょう。

```bash
By default, a MySQL installation has an anonymous user,
allowing anyone to log into MySQL without having to have
a user account created for them. This is intended only for
testing, and to make the installation go a bit smoother.
You should remove them before moving into a production
environment.

Remove anonymous users? (Press y|Y for Yes, any other key for No) : y
Success.
```

rootユーザでリモートのログイン許可の可否について聞かれています。
基本的にlocalhostでしか使用しないので、必要ありません。
yを押しましょう。

```bash
Normally, root should only be allowed to connect from
'localhost'. This ensures that someone cannot guess at
the root password from the network.

Disallow root login remotely? (Press y|Y for Yes, any other key for No) : y
Success.
```

デフォルトでtestのDBが作成されています。
必要ないので削除しましょう。
yを押して進めます。

```bash
By default, MySQL comes with a database named 'test' that
anyone can access. This is also intended only for testing,
and should be removed before moving into a production
environment.


Remove test database and access to it? (Press y|Y for Yes, any other key for No) : y
 - Dropping test database...
Success.
 - Removing privileges on test database...
Success.
```

DBに更新があった場合、即時リロードして反映するかという説明です。
yを押して有効にしましょう。

```bash
Reloading the privilege tables will ensure that all changes
made so far will take effect immediately.

Reload privilege tables now? (Press y|Y for Yes, any other key for No) : y
Success.

All done!
```

All done!と表示されたら終了です。

パスワードの設定が完了し、正常に利用が可能かどうかを確認するために、以下のコマンドを実行します。

なお、`以後ターミナルからmysqlを利用するときは、毎回以下のコマンドを実行する`ことを覚えておいてください。

```bash
mysql -root -p
```

以下のパスワード入力状態になりましたら、自身で決めたパスワードを入力します。

```bash
Enter password:
```

パスワードがOKでしたら以下の画面が表示され、SQLが入力できるようになります。

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

これで、インストールが完了しました。mysqlのコマンドの入力モードを終了する際は、以下のコマンドを実行します。

```bash
mysql> exit;
```

以上です。お疲れさまでした。


[laravelチュートリアルに戻る](/web_application/laravel_tutorial.md) /
[トップへ](/README.md)