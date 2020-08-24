## Laravel補足情報

### .envファイル
sampleディレクトリ直下には、「.env」というファイルがあります。
アプリケーションの環境設定情報を記述するファイルです。
具体的には暗号化キーやデータベースの接続情報を記述します。

> GitHubなどPublicなリポジトリを利用される場合は扱いに注意しましょう。

### artisanファイル
sampleディレクトリ直下に「artisan」というファイルがあります。
artisanファイルはLaravelのコマンドラインツールであるArtisanの実行ファイルです。
LaravelではArtisanコマンドを使用して様々な操作を行います。主なものを紹介します。

1. serveコマンド

PHPの組み込みサーバーで、Laravelプロジェクトを動作させます。
自端末（ローカル）で動作させる際に実行します。

```bash
% php artisan serve --host=localhost --port=8000
Laravel development server started: <http://localhost:8000>
```
> --hostオプションと--portオプションは省略できます。


2. route:listコマンド

ルーティング定義の一覧を表示します。

```bash
% php artisan route:list
+--------+----------+----------+------+---------+--------------+
| Domain | Method   | URI      | Name | Action  | Middleware   |
+--------+----------+----------+------+---------+--------------+
|        | GET|HEAD | /        |      | Closure | web          |
|        | GET|HEAD | api/user |      | Closure | api,auth:api |
+--------+----------+----------+------+---------+--------------+
```

例えば1行目は、ルート（http://localhost:8000 ）でGETリクエストを受け付けていることを表しています。
詳しくは後ほど使いながら見てみましょう。

3. tinkerコマンド

プロジェクトをREPL(Read Eval Print Loop）で起動します。
REPLとは、打ち込んだコードが即時に結果を返してくれる仕組みのことを言います。

```bash
% php artisan tinker
Psy Shell v0.9.9 (PHP 7.1.16 — cli) by Justin Hileman
>>> $name = 'Laravel';
=> "Laravel"
>>> echo 'Hello ' . $name;
Hello Laravel⏎
>>>
```

プロジェクト内のオブジェクトがどんなメソッドを持っていたかなど簡単に確認できます。
tinkerはデバッグ時に力を発揮します。「ビューの作成」の章で説明します。


[laravelチュートリアルに戻る](/web_application/laravel_tutorial.md) /
[トップへ](/README.md)