# Laravel入門　使い方チュートリアル
## Laravelのインストール

Laravelのインストールには様々な方法がありますが、ここではComposerを使用します。
create-projectコマンドを使用することで、インストールと同時にプロジェクトを作成することができます。

```bash
    % composer create-project laravel/laravel sample
```

実行が完了すると「Application key set successfully.」と表示されます。
その後、lsコマンドで「sample」フォルダが作成されていることを確認したら、ディレクトリを移動します

```bash
    % cd sample
```

以降はsampleディレクトリで作業をしていきます。

## Laravelプロジェクトのディレクトリ構成

Laravelプロジェクトを作成すると、以下のようなディレクトリを構成します。
```bash
/sample
    ├── app       ・・・アプリケーションのロジック
    ├── bootstrap ・・・laravelフレームワークの起動コード
    ├── config    ・・・設定ファイル
    ├── database  ・・・MigrationファイルなどDB関連
    ├── public    ・・・Webサーバのドキュメントルート
    ├── resources ・・・ビューや言語変換用ファイルなど
    ├── routes    ・・・ルーティング用ファイル
    ├── storage   ・・・フレームワークが使用するファイル
    ├── tests     ・・・テストコード
    └── vendor    ・・・Composerでインストールしたライブラリ
```

> Laravelのバージョンによって構成が異なる場合があります。

この構成に従ってWebアプリケーションを作っていくことになります。
いくつか重要なファイルがあるので、実際に使用する前に簡単に紹介します。

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

## データベース設定

Laravelプロジェクトを作成したら、まずデータベースの設定をします。
最初は以下の作業が必要になります。

- 接続設定
- マイグレーション
- モデル作成
- シーディング

### 接続設定

データベースへの接続情報は.envファイルに記述します。
初期値では以下のようになっています。自身の環境に合わせて設定してください。

```bash
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=homestead
DB_USERNAME=homestead
DB_PASSWORD=secret
```

>上記の場合、ローカルにMySQLをインストールし、homesteadデータベースを作成、homeseteadというユーザーをsecretというパスワードで作成すればOKです

## マイグレーション

マイグレーションとは、テーブルの作成や編集などをSQLによって行うのではなく、PHPのソースで管理する仕組みです。
次の手順で行います。

1. マイグレーションファイルの作成
1. マイグレーションの実行

### 1. マイグレーションファイルの作成

マイグレーションファイルの作成にはArtisanのmake:migrationコマンドを使用します。
ファイル名は何でも構いませんが、どのような操作を行ったのかわかる名前をつけると良いでしょう。
Booksテーブルを作成するマイグレーションファイルを作成します。

```bash
% php artisan make:migration create_books_table --create=books
Created Migration: 2018_10_30_085034_create_books_table
```

マイグレーションファイルはdatabase/migrationsディレクトリに作成されます。

> database/migrations/2018_10_30_085034_create_books_table.php
```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateBookTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('books', function (Blueprint $table) {
            $table->id();
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('books');
    }
}

```

マイグレーションファイルではupメソッドにテーブル作成時の情報、downメソッドにマイグレーションを取り消す際の情報を記述します。
現在の内容は、主キーとなるIDと、タイムスタンプを持つBooksテーブルを作成、取り消し時はBooksテーブルを削除するという内容になっています。以下のようなテーブルを作成したいとしましょう。

|物理名|型|制約|論理名|
|:--:|:--:|:--:|:--:|
|id|int|auto_increment|ID|
|title|string|NOT NULL|タイトル|
|author|string|NOT NULL|著者|
|price|int|NOT NULL|価格|
|comment|text||コメント|
|created_at|timestamp||作成日|
|updated_at|timestamp||更新日|

この場合、upメソッドを以下のように書き換えます。
>database/migrations/yyyy_mm_dd_hhssmm_create_books_table.php
```php
public function up('books', funtion (Blueprint $table){
    $table->increments('id');
    $table->string('title', 100);
    $table->string('author', 50);
    $table->integer('price');
    $table->text('comment')->nullable();
    $table->timestamps();
})
```

この記述方法はスキーマビルダと呼ばれています。
詳細は公式ドキュメントを参考にしてください。
https://readouble.com/laravel/5.0/ja/schema.html

これでマイグレーションファイルの作成が完了しました。

### 2. マイグレーションの実行

マイグレーションの実行にはartisanのmigrateコマンドを実行します。


```bash
php artisan migrate
Migration table created successfully.
Migrating: 2014_10_12_000000_create_users_table
Migrated:  2014_10_12_000000_create_users_table (0.06 seconds)
Migrating: 2019_08_19_000000_create_failed_jobs_table
Migrated:  2019_08_19_000000_create_failed_jobs_table (0.02 seconds)
Migrating: 2020_08_03_105918_create_book_table
Migrated:  2020_08_03_105918_create_book_table (0.02 seconds)
```

これで、MySQLサーバー上にテーブルが作成されます。

```sql
+------------------+
| Tables_in_sample |
+------------------+
| books            |
| failed_jobs      |
| migrations       |
| users            |
+------------------+
```

> ※usersテーブルとfailed_jobsテーブルのマイグレーションファイルはプロジェクト作成時に自動的に作成されたものです。今回は無視します。
migrationsテーブルにはマイグレーションの情報が保存されます。


booksテーブルが指定された通りに作成されているか確認しましょう。

```sql
+------------+------------------+------+-----+---------+----------------+
| Field      | Type             | Null | Key | Default | Extra          |
+------------+------------------+------+-----+---------+----------------+
| id         | int(10) unsigned | NO   | PRI | NULL    | auto_increment |
| title      | varchar(100)     | NO   |     | NULL    |                |
| author     | varchar(50)      | NO   |     | NULL    |                |
| price      | int(11)          | NO   |     | NULL    |                |
| comment    | text             | YES  |     | NULL    |                |
| created_at | timestamp        | YES  |     | NULL    |                |
| updated_at | timestamp        | YES  |     | NULL    |                |
+------------+------------------+------+-----+---------+----------------+
```

### 補足

マイグレーションを取り消したい場合はmigrate:rollbackコマンド、またはmigrate:resetコマンドを使用します。
migrate:rollbackコマンドは直前のマイグレーションのみ取り消します。

```bash
% php artisan migrate:rollback
Rolling back: 2018_10_30_085034_create_books_table
Rolled back:  2018_10_30_085034_create_books_table
```

migrate:resetは全てのマイグレーションを取り消します。

```bash
% php artisan migrate:reset
Rolling back: 2018_10_30_085034_create_books_table
Rolled back:  2018_10_30_085034_create_books_table
```

## モデルの作成

モデルはテーブルとマッピングされたオブジェクトです。
DB操作を行うためのクラスになります。
詳しくは「Eloquent ORM」の章で説明します。
モデルはArtisanの`make:model`コマンドで作成します。

```bash
% php artisan make:model Book
Model created successfully.
```

モデルはAppディレクトリ直下に作成されます。
> App/Book.php

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Book extends Model
{
    //
}
```

中身は空ですが、このままで構いません。

### モデルの命名規則
モデルは命名規則によってテーブルとマッピングされます。
テーブル名の単数形を名前につけることで、**自動的にそのテーブルとマッピング**します。

## シーディング
シーディングは、テストデータやマスタデータなどのアプリケーション起動時に必要なレコードをコマンドで登録する仕組みです。
次の手順で実行します。

1. シーダーファイルの作成
1. シーディングの実行

### **1.シーダーファイルの作成**

シーダーファイルはArtisanの`make:seeder`コマンドを使用して作成します。

```bash
% php artisan make:seeder BooksTableSeeder
Seeder created successfully.
```

`database/seeds`ディレクトリにBooksTableSeeder.phpが作成されます。
> database/seeds/BooksTableSeeder.php
```php
<?php

use Illuminate\Database\Seeder;

class BooksTableSeeder extends Seeder
{
    /**
     * Run the database seeds.
     *
     * @return void
     */
    public function run()
    {
        //
    }
}
```

Booksテーブルに、以下のレコードを登録したいとしましょう。

|id|title|author|price|comment|created_at|updated_at|
|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
|1|PHP Book|山田 太郎|2000|PHP本で一番おすすめ|現在時刻|現在時刻|
|2|Laravel Book|東京 次郎|3000|Laravel本で一番わかりやすい|現在時刻|現在時刻|
|3|日本一わかりやすいPHPとLaravel|Tech Quest社|3500|日本で一番売れている本|現在時刻|現在時刻|

その場合は、シーダーファイルのrunメソッドを以下のように修正します。
> database/seeds/BooksTableSeeder.php

```php
<?php

use Illuminate\Database\Seeder;

class BooksTableSeeder extends Seeder
{
    /**
     * Run the database seeds.
     *
     * @return void
     */
    public function run()
    {
        // booksテーブルのデータをクリアする
        DB::table('books')->truncate();

        // 初期データを用意する（列名をキーとする連想配列で用意する
        $books = [
            [
                'title' => 'PHP Book',
                'author' => '山田 太郎',
                'price' => 2000,
                'comment' => 'PHPで一番オススメ',
            ],
            [
                'title' => 'Laravel Book',
                'author' => '東京 次郎',
                'price' => 3000,
                'comment' => 'Laravel本で一番わかりやすい',
            ],
            [
                'title' => '日本一わかりやすいPHPとLaravel',
                'author' => 'Tech Quest社',
                'price' => 3500,
                'comment' => '日本で一番売れている本',
            ]
        ];

        // テストデータを登録する
        foreach ($books as $book) {
            \App\Book::create($book);
        }
    }
}
```

さらにDatabaseSeeder.phpのrunメソッドを以下のように修正します。runメソッド内でcallしたクラスが、シーディングコマンドで実行されるようになります。
> database/seeds/DatabaseSeeder.php
```php
public function run()
{
     // BooksTableSeederを読み込むように指定
     $this->call(BooksTableSeeder::class);
}
```

## 2.シーディングの実行
シーディングを実行するにはArtisanの`db:seed`コマンドを実行します。

```bash
% php artisan db:seed
Seeding: BooksTableSeeder
Database seeding completed successfully.
```

完了したらテーブルを確認しましょう。

```sql
mysql> select * from books;
+----+------------------------------------------+---------------+-------+---------------------------------------+---------------------+---------------------+
| id | title                                    | author        | price | comment                               | created_at          | updated_at          |
+----+------------------------------------------+---------------+-------+---------------------------------------+---------------------+---------------------+
|  1 | PHP Book                                 | 山田 太郎      |  2000 | PHPで一番オススメ                        | 2020-08-04 04:51:18 | 2020-08-04 04:51:18 |
|  2 | Laravel Book                             | 東京 次郎      |  3000 | Laravel本で一番わかりやすい               | 2020-08-04 04:51:18 | 2020-08-04 04:51:18 |
|  3 | 日本一わかりやすいPHPとLaravel              | Tech Quest社　 |  3500 | 日本で一番売れている本                    | 2020-08-04 04:51:18 | 2020-08-04 04:51:18 |
+----+------------------------------------------+---------------+-------+---------------------------------------+---------------------+---------------------+
3 rows in set (0.01 sec)
```

以上でデータベース関連の設定は完了です。
作業量が多かったかもしれませんが一度作ってしまえば、プロジェクトを共有しているユーザー全てが`migrate`と`db:seed`の2つのコマンドのみで、DBの初期化を行うことができます。

## ルーティング
データベースの準備が整ったらアプリケーションを作成します。
まずはルーティングから行なっていきましょう。
ルーティングとは、クライアントからのリクエストを受け付け、その内容によって処理を振り分けることです。
ルーティング情報は`routes/web.php`ファイルに記述します。

> routes/web.php
```php
<?php
Route::get('/', function () {
    return view('welcome');
});
```

すでに一つルーティングが定義されています。
これは、ルート(/)にGETリクエストがきた場合に、welcomeというビューをレスポンスとして返すという意味です。
welcomeは`resources/views/welcome.blade.php`のことです。

> Laravelはview関数を呼び出すと、resources/views/ディレクトリを探しにいきます。
その中からファイル名（拡張子除く）が一致するものを返す仕組みとなっています。

Artisanコマンドでサーバーを立ち上げ、http://localhost:8000/ にアクセスしてみるとwelcome.blade.phpの内容が表示されます。

なお、上記のように直接ビューを返すのではなく、コントローラを経由するようにルーティングするには以下のように記述します。

> routes/web.php
```php
<?php
Route::get('book', 'BookController@index');
```

上記のように記述した場合、「localhost:8000/bookにGETリクエストがきたら、BookControllerのindexメソッドに処理を振り分けて」という意味になります。この段階ではまだBookControllerを作成していないので正常に動作しません。

>BookControllerは「Controllerの作成」の章で作成します。

## ルートパラメータ
コントローラを作る前にルートパラメータについても知っておきましょう。

> routes/web.php
```php
<?php
Route::get('book/{id}', 'BookController@show');
```

上記のように記述した場合、「localhost:8000/book/1にGETリクエストがきたら、BookControllerのshowメソッドに処理を振り分けて」という意味になります。
BookControllerではshowメソッドに引数を持たせるようにします。

> app/Http/Controllers/BookController.php(未作成)
```php
public function show($id)
{
    return view('book', ['book' => Book::findOrFail($id)]);
}
```

URIに指定された「1」という数字が$idという引数の中に代入されます。


## Restfulリソースコントローラ
Route::resourceメソッドを使用することで、Restfulなルーティングを行うことができます。

```php
<?php
Route::resource('book', 'BookController');
```

|リクエストメソッド|URI|コントローラー|CRUD画面を作る際の主な用途|
|:--:|:--:|:--:|:--:|
|GET|/book|BookController@index|一覧画面の表示|
|GET|/book/{book}|BookController@show|詳細画面の表示|
|GET|/book/create|BookController@create|登録画面の表示|
|POST|/book|BookController@store|登録処理|
|GET|/book/{book}/edit|BookController@edit|編集画面の表示|
|PUT|/book/{book}|BookController@update|編集処理|
|DELETE|/book/{book}|BookController@destroy|削除処理|

> 以降、resourceメソッドでルーティングしている前提でコントローラを作成します。

## コントローラの作成
ルーティングを設定したら次はコントローラを作成します。
コントローラはルーティングされてきたリクエストを受け取り、レスポンスを作成する役割を果たします。
レスポンスとしてHTMLを返す場合はViewに処理を依頼します。
コントローラの作成にはArtisanの`make:controller`コマンドを使用します。

```php
% php artisan make:controller BookController
Controller created successfully.
```

コントローラは`app/Http/Controllers`ディレクトリに作成されます。
以下のようにindexメソッドとeditメソッドを追加してください。
`use App/Book`の記述を忘れないようにしてください。

> app/Http/Controllers/BookController.php

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Http\Controllers\Controller;
use App\Book;

class BookController extends Controller
{
  public function index()
  {
      // DBよりBookテーブルの値を全て取得
      $books = Book::all();

      // 取得した値をビュー「book/index」に渡す
      return view('book/index', compact('books'));
  }

  public function edit($id)
  {
      // DBよりURIパラメータと同じIDを持つBookの情報を取得
      $book = Book::findOrFail($id);

      // 取得した値をビュー「book/edit」に渡す
      return view('book/edit', compact('book'));
  }
}
```

コントローラのメソッドでビューを返したい場合、view関数を使用します。
view関数は第一引数にビューの名前、第二引数にビューに渡したい値を設定します。
第二引数は連想配列で渡すようにします。
compact関数を使うことで簡単に連想配列を渡すことができます。
例えば、edit関数の`compact('book')`は`['book' => $book]`としているのと同じ意味です。

ルーティングされているメソッドのうち、とりあえずindexメソッドとeditメソッドを用意しました。
DBからの値取得はEloquentという仕組みを利用しています。
Eloquentに関しては「Eloquent ORM」の章で説明します。

## ビューの作成

では最後にビューを作成しましょう。
ビューは`resources/views`ディレクトリに作成します。
bookディレクトリを作成し、その中にindex.blade.phpを作成しましょう。

> resources/views/book/index.blade.php

```php
<head>
  <title>Laravel Sample</title>
  <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css">
</head>
<div class="container ops-main">
    <div class="row">
        <div class="col-md-12">
            <h3 class="ops-title">書籍一覧</h3>
        </div>
    </div>
    <div class="row">
        <div class="col-md-11 col-md-offset-1">
            <table class="table text-center">
                <tr>
                    <th class="text-center">ID</th>
                    <th class="text-center">書籍名</th>
                    <th class="text-center">価格</th>
                    <th class="text-center">著者</th>
                    <th class="text-center">削除</th>
                </tr>
                @foreach($books as $book)
                <tr>
                    <td>
                        <a href="/book/{{ $book->id }}/edit">{{ $book->id }}</a>
                    </td>
                    <td>{{ $book->title }}</td>
                    <td>{{ $book->author }}</td>
                    <td>{{ $book->price }}</td>
                    <td>{{ $book->comment }}</td>
                    <td>
                        <form action="/book/{{ $book->id }}" method="post">
                            <input type="hidden" name="_method" value="DELETE">
                            <input type="hidden" name="_token" value="{{ csrf_token() }}">
                            <button type="submit" class="btn btn-xs btn-danger" aria-label="Left Align"><span
                                    class="glyphicon glyphicon-trash"></span></button>
                        </form>
                    </td>
                </tr>
                @endforeach
            </table>
           <div><a href="/book/create" class="btn btn-default">新規作成</a></div>
        </div>
    </div>
</div>
```

Laravelではビューの作成にBladeというテンプレートエンジンを用いています。
Bladeでは{{ $variables }}とすることで、コントローラから受け取った値を画面に出力することができます。
また@foreach($array as $elem)や@if($bool)と記述することで制御構文を使用することもできます。
その場合、制御構文の終端は@endforeach、@endifと記述します。

Bladeの詳細に関しては以下のサイトで確認してください。

https://readouble.com/laravel/5.7/ja/blade.html

ここまで完成したら、Artisanでサーバーを立ち上げ、http://localhost:8000/book にアクセスしてみましょう。
一覧画面が表示されるはずです。

ここで一度流れを整理しておきましょう。
`route:list`コマンドでルーティングを再確認します。

```bash
php artisan route:list
+--------+-----------+------------------+--------------+---------------------------------------------+------------+
| Domain | Method    | URI              | Name         | Action                                      | Middleware |
+--------+-----------+------------------+--------------+---------------------------------------------+------------+
|        | GET|HEAD  | /                |              | Closure                                     | web        |
|        | GET|HEAD  | api/user         |              | Closure                                     | api        |
|        |           |                  |              |                                             | auth:api   |
|        | GET|HEAD  | book             | book.index   | App\Http\Controllers\BookController@index   | web        |
|        | POST      | book             | book.store   | App\Http\Controllers\BookController@store   | web        |
|        | GET|HEAD  | book/create      | book.create  | App\Http\Controllers\BookController@create  | web        |
|        | GET|HEAD  | book/{book}      | book.show    | App\Http\Controllers\BookController@show    | web        |
|        | PUT|PATCH | book/{book}      | book.update  | App\Http\Controllers\BookController@update  | web        |
|        | DELETE    | book/{book}      | book.destroy | App\Http\Controllers\BookController@destroy | web        |
|        | GET|HEAD  | book/{book}/edit | book.edit    | App\Http\Controllers\BookController@edit    | web        |
+--------+-----------+------------------+--------------+---------------------------------------------+------------+
```

今、ブラウザからhttp://localhost:8000/book にアクセスしました。
つまりGETリクエストでbookにアクセスしたことになります。
その場合、BookControllerのindexメソッドが呼び出されるように設定されていました。


```bash
+--------+-----------+------------------+--------------+---------------------------------------------+------------+
| Domain | Method    | URI              | Name         | Action                                      | Middleware |
+--------+-----------+------------------+--------------+---------------------------------------------+------------+
|        | GET|HEAD  | /                |              | Closure                                     | web        |
|        | GET|HEAD  | api/user         |              | Closure                                     | api        |
|        |           |                  |              |                                             | auth:api   |
|        | GET|HEAD  | book             | book.index   | App\Http\Controllers\BookController@index   | web        |
+--------+-----------+------------------+--------------+---------------------------------------------+------------+
```

そのため、BookControllerのindexメソッドが呼び出されます。
indexメソッドでは、書籍の一覧情報を取得し、resources/views/book/index.blade.phpに渡しました。

> app/Http/Controllers/BookController.php
```php
public function index()
{
    // DBよりBookテーブルの値を全て取得
    $books = Book::all();

    // 取得した値をビュー「book/index」に渡す
    return view('book/index', compact('books'));
}
```

最後にindex.blade.phpでは受け取った値を元に画面を生成し、それが最終的にブラウザに表示されたというわけです。

では、次にedit.blade.phpを作成しましょう。今の流れを意識しながら作成してください。
> resources/views/book/edit.blade.php

```php
<head>
    <title>Laravel Sample</title>
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css">
</head>
<div class="container ops-main">
    <div class="row">
        <div class="col-md-6">
            <h2>書籍登録</h2>
        </div>
    </div>
    <div class="row">
        <div class="col-md-8 col-md-offset-1">
            <form action="/book/{{ $book->id }}" method="post">
                <!-- updateメソッドにはPUTメソッドがルーティングされているのでPUTにする -->
                <input type="hidden" name="_method" value="PUT">
                <input type="hidden" name="_token" value="{{ csrf_token() }}">
                <div class="form-group">
                    <label for="title">書籍名</label>
                    <input type="text" class="form-control" name="title" value="{{ $book->title }}">
                </div>
                <div class="form-group">
                    <label for="author">著者名</label>
                    <input type="text" class="form-control" name="author" value="{{ $book->author }}">
                </div>
                <div class="form-group">
                    <label for="price">価格</label>
                    <input type="text" class="form-control" name="price" value="{{ $book->price }}">
                </div>
                <div class="form-group">
                    <label for="comment">コメント</label>
                    <input type="text" class="form-control" name="comment" value="{{ $book->comment }}">
                </div>
                <button type="submit" class="btn btn-default">登録</button>
                <a href="/book">戻る</a>
            </form>
        </div>
    </div>
</div>
```

これで編集画面が作成できました。書籍一覧のIDをクリックすると、編集画面に遷移します。

## ビューの継承

ところで、ヘッダー部分は書籍一覧画面と書籍登録画面で同じですね。
こういう時、ビューの継承を利用すると効率的に作成することができます。
親ビューになるlayout.blade.phpを作成します。共通となる要素のみ記述します。

> views/book/layout.blade.php
```php
<head>
    <title>Laravel Sample</title>
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css">
</head>
@yield('content')
```

次にindex.blade.phpを書き換えます。

```php
@extends('book/layout')
@section('content')
<div class="container ops-main">
<div class="row">
    <div class="col-md-12">
        <h3 class="ops-title">Books</h3>
    </div>
</div>
<div class="row">
  <!-- 中略 -->
</div>
@endsection
```

共通要素を消去し、代わりに@extends('book/layout')を記述します。
これで、layout.blade.phpを継承することを宣言したことになります。
すると、layoutの@yield('content')部分に@section('content')〜@endsectionで囲った部分が挿入されます。
では、edit.blade.phpにもlayoutを継承させましょう。

```php
@extends('book/layout')
@section('content')
<div class="container ops-main">
    <div class="row">
        <div class="col-md-6">
            <h2>書籍登録</h2>
        </div>
    </div>
    <!-- 中略 -->
</div>
@endsection
```

共通のCSSの読み込みや、フッター、ヘッダーなどをlayout.blade.phpに記入することで、継承している全ての画面に反映させることができます。
次はDB操作の処理を実装していくことになりますが、その前にEloquentについて学びましょう。

## Eloquent ORM

EloquentはLaravelでデータ操作をするための実装です。
Bookモデルというのを作成したのを思い出してください。
BookモデルはBookテーブルにマッピングされており、テーブルの登録や取得更新などの操作を持っています。
Bookモデルとした場合、特に指定しなければ命名規則によりbooksテーブルとマッピングされます。

### レコードの取得
モデルにマッピングされたテーブルの全てのレコードを取得するにはallメソッドを利用します。
tinkerを立ち上げてDB操作してみましょう。
`use \App\Book;`の入力を忘れないようにしてください。

```bash
$ php artisan tinker
Psy Shell v0.10.4 (PHP 7.2.26 — cli) by Justin Hileman
>>> use \App\Book;
>>> $books = Book::all();
=> Illuminate\Database\Eloquent\Collection {#4018
     all: [
       App\Book {#4019
         id: 1,
         title: "PHP Book",
         author: "山田 太郎",
         price: 2000,
         comment: "PHPで一番オススメ",
         created_at: "2020-08-04 04:51:18",
         updated_at: "2020-08-04 04:51:18",
       },
       App\Book {#4020
         id: 2,
         title: "Laravel Book",
         author: "東京 次郎",
         price: 3000,
         comment: "Laravel本で一番わかりやすい",
         created_at: "2020-08-04 04:51:18",
         updated_at: "2020-08-04 04:51:18",
       },
       App\Book {#4021
         id: 3,
         title: "日本一わかりやすいPHPとLaravel",
         author: "Tech Quest社",
         price: 3500,
         comment: "日本で一番売れている本",
         created_at: "2020-08-04 04:51:18",
         updated_at: "2020-08-04 04:51:18",
       },
     ],
   }
>>>
```

countメソッドやforeachを利用することができます。

```bash
>>> $books->count();
=> 3
```

### 取得条件の設定

取得レコードに条件を設定する場合はwhereメソッドを使います。

```bash
>>> $expensiveBooks = Book::where('price', '>=', 2500)->get();
=> Illuminate\Database\Eloquent\Collection {#4007
     all: [
       App\Book {#4006
         id: 2,
         title: "Laravel Book",
         author: "東京 次郎",
         price: 3000,
         comment: "Laravel本で一番わかりやすい",
         created_at: "2020-08-04 04:51:18",
         updated_at: "2020-08-04 04:51:18",
       },
       App\Book {#4001
         id: 3,
         title: "日本一わかりやすいPHPとLaravel",
         author: "Tech Quest社",
         price: 3500,
         comment: "日本で一番売れている本",
         created_at: "2020-08-04 04:51:18",
         updated_at: "2020-08-04 04:51:18",
       },
     ],
   }
>>>
```

単一行の取得であれば、findメソッドを使うことで効率的に記述できます。
findメソッドは主キーによる検索を行います。

```bash
>>> $book = $books->find(2);
=> App\Book {#4020
     id: 2,
     title: "Laravel Book",
     author: "東京 次郎",
     price: 3000,
     comment: "Laravel本で一番わかりやすい",
     created_at: "2020-08-04 04:51:18",
     updated_at: "2020-08-04 04:51:18",
   }
>>>
```

### レコードの登録
登録するにはモデルのインスタンスを作成し、saveメソッドを呼び出します。

```bash
>>> $newBook = new Book();
=> App\Book {#4029}
>>> $newBook->title = 'Cake PHP入門';
=> "Cake PHP入門"
>>> $newBook->author = '神奈川　健太郎';
=> "神奈川　健太郎"
>>> $newBook->price = 1980;
=> 1980
>>> $newBook->comment = 'Cake PHPの学習ならこれがおすすめ！'
=> "Cake PHPの学習ならこれがおすすめ！"
>>> $newBook->save();
=> true
>>> $cakePhpBook = Book::find(4);
=> App\Book {#4030
     id: 4,
     title: "Cake PHP入門",
     author: "神奈川　健太郎",
     price: 1980,
     comment: "Cake PHPの学習ならこれがおすすめ！",
     created_at: "2020-08-05 05:29:46",
     updated_at: "2020-08-05 05:29:46",
   }
>>>
```

## レコードの更新
更新はモデルを取得後、プロパティを変更してsaveメソッドを呼び出します。

```bash
>>> $cakePhpBook;
=> App\Book {#4030
     id: 4,
     title: "Cake PHP入門",
     author: "神奈川　健太郎",
     price: 1980,
     comment: "Cake PHPの学習ならこれがおすすめ！",
     created_at: "2020-08-05 05:29:46",
     updated_at: "2020-08-05 05:29:46",
   }
>>> $cakePhpBook->author = '埼玉 五郎';
=> "埼玉 五郎"
>>> $cakePhpBook->save();
=> true
>>> $cakePhpBook = Book::find(4);
=> App\Book {#4011
     id: 4,
     title: "Cake PHP入門",
     author: "埼玉 五郎",
     price: 1980,
     comment: "Cake PHPの学習ならこれがおすすめ！",
     created_at: "2020-08-05 05:29:46",
     updated_at: "2020-08-05 05:35:20",
   }
>>>
```

複数件の更新も可能です。

```bash
>>> Book::where('price', '>=', 3000)->update(['price' => 3300]);
=> 2
>>> Book::all();
=> Illuminate\Database\Eloquent\Collection {#4027
     all: [
       App\Book {#4033
         id: 1,
         title: "PHP Book",
         author: "山田 太郎",
         price: 2000,
         comment: "PHPで一番オススメ",
         created_at: "2020-08-04 04:51:18",
         updated_at: "2020-08-04 04:51:18",
       },
       App\Book {#4030
         id: 2,
         title: "Laravel Book",
         author: "東京 次郎",
         price: 3300,
         comment: "Laravel本で一番わかりやすい",
         created_at: "2020-08-04 04:51:18",
         updated_at: "2020-08-05 05:37:40",
       },
       App\Book {#4015
         id: 3,
         title: "日本一わかりやすいPHPとLaravel",
         author: "Tech Quest社",
         price: 3300,
         comment: "日本で一番売れている本",
         created_at: "2020-08-04 04:51:18",
         updated_at: "2020-08-05 05:37:40",
       },
       App\Book {#4045
         id: 4,
         title: "Cake PHP入門",
         author: "埼玉 五郎",
         price: 1980,
         comment: "Cake PHPの学習ならこれがおすすめ！",
         created_at: "2020-08-05 05:29:46",
         updated_at: "2020-08-05 05:35:20",
       },
     ],
   }
>>>
```

### レコードの削除
削除にはdeleteメソッドを使います。

```bash
>>> $cakePhpBook;
=> App\Book {#4011
     id: 4,
     title: "Cake PHP入門",
     author: "埼玉 五郎",
     price: 1980,
     comment: "Cake PHPの学習ならこれがおすすめ！",
     created_at: "2020-08-05 05:29:46",
     updated_at: "2020-08-05 05:35:20",
   }
>>> $cakePhpBook->delete();
=> true
>>> $cakePhpBook->find(4);
=> null
>>>
```

## CRUDの実装

では残していた更新、登録、削除の機能を実装していきましょう。
まずは更新を作成します。

### 更新処理
編集画面はできているので、コントローラにupdateメソッドを定義します。

> app/Http/Controllers/BookController.php

```php
public function update(Request $request, $id)
    {
        $book = Book::findOrFail($id);
        $book->title = $request->title;
        $book->author = $request->author;
        $book->price = $request->price;
        $book->comment = $request->comment;
        $book->save();

        return redirect("/book");
    }
```

pdateメソッドの$idにはURI：`book/{book}`の{book}の部分の値が代入されます。
つまり、http://localhost:8000/book/3 にPUTメソッドでアクセスしたら、updateメソッドが呼び出され、idに3が代入されます。
requestはクライアントからのリクエスト情報が入っています。
クライアントが入力した値を取得する場合は`$request->[要素名]`で取得します。

### 削除処理
書籍一覧画面にすでに削除ボタンは作成しているので、コントローラにdestroyメソッドを定義します。

> app/Http/Controllers/BookController.php

```php
public function destroy($id)
{
    $book = Book::findOrFail($id);
    $book->delete();

    return redirect("/book");
}
```

### 登録処理

最後に登録処理ですが、画面はedit.blade.phpとほぼ同じなので再利用するようにしましょう。
まず`views/book`ディレクトリに共通部分を記述するform.blade.phpを作成します。
edit.blade.phpの@section内部を全て移動させます。

> resources/views/book/form.blade.php
```php
<div class="container ops-main">
    <div class="row">
        <div class="col-md-6">
            <h2>書籍登録</h2>
        </div>
    </div>
    <div class="row">
        <div class="col-md-8 col-md-offset-1">
            <form action="/book/{{ $book->id }}" method="post">
                <!-- updateメソッドにはPUTメソッドがルーティングされているのでPUTにする -->
                <input type="hidden" name="_method" value="PUT">
                <input type="hidden" name="_token" value="{{ csrf_token() }}">
                <div class="form-group">
                    <label for="name">書籍名</label>
                    <input type="text" class="form-control" name="name" value="{{ $book->title }}">
                </div>
                <div class="form-group">
                    <label for="name">著者名</label>
                    <input type="text" class="form-control" name="name" value="{{ $book->author }}">
                </div>
                <div class="form-group">
                    <label for="price">価格</label>
                    <input type="text" class="form-control" name="price" value="{{ $book->price }}">
                </div>
                <div class="form-group">
                    <label for="comment">コメント</label>
                    <input type="text" class="form-control" name="author" value="{{ $book->comment }}">
                </div>
                <button type="submit" class="btn btn-default">登録</button>
                <a href="/book">戻る</a>
            </form>
        </div>
    </div>
</div>
```

次に新規登録時と更新時で異なる部分だけ`@if`を使用して分岐させます。
今回の場合、formタグ部分だけ書き換えればOKです。

>resources/views/book/form.blade.php

```php
<div class="container ops-main">
    <div class="row">
        <div class="col-md-6">
            <h2>書籍登録</h2>
        </div>
    </div>
    <div class="row">
        <div class="col-md-8 col-md-offset-1">
            @if($target == 'store')
            <form action="/book" method="post">
                @elseif($target == 'update')
                <form action="/book/{{ $book->id }}" method="post">
                    <!-- updateメソッドにはPUTメソッドがルーティングされているのでPUTにする -->
                    <input type="hidden" name="_method" value="PUT">
                    @endif
                    <input type="hidden" name="_token" value="{{ csrf_token() }}">
                    <div class="form-group">
                        <label for="title">書籍名</label>
                        <input type="text" class="form-control" name="title" value="{{ $book->title }}">
                    </div>
                    <div class="form-group">
                        <label for="author">著者名</label>
                        <input type="text" class="form-control" name="author" value="{{ $book->author }}">
                    </div>
                    <div class="form-group">
                        <label for="price">価格</label>
                        <input type="text" class="form-control" name="price" value="{{ $book->price }}">
                    </div>
                    <div class="form-group">
                        <label for="comment">コメント</label>
                        <input type="text" class="form-control" name="comment" value="{{ $book->comment }}">
                    </div>
                    <button type="submit" class="btn btn-default">登録</button>
                    <a href="/book">戻る</a>
                </form>
        </div>
    </div>
</div>
```

次はedit.blade.phpを以下のように書き換えます。

> resources/views/book/edit.blade.php
```php
@extends('book/layout')
@section('content')
@include('book/form', ['target' => 'update'])
@endsection
```

`@include`を使用して、form.blade.phpを`@section`内に挿入します。
その際、target変数にupdateという値を渡します。

では、新しく`views/book/`ディレクトリにcreate.blade.phpを作成しましょう。

> resources/views/book/create.blade.php

```php
@extends('book/layout')
@section('content')
@include('book/form', ['target' => 'store'])
@endsection
```

targetをstoreに書き換えるだけです。
これで、新規登録の時と編集の時で、formの内容が分岐される仕組みができます。
登録時のコントローラが未作成ですのでcreateメソッドとstoreメソッドを追加しましょう。

```php
public function create()
    {
        $book = new Book();
        return view('book/create', compact('book'));
    }

    public function store(Request $request)
    {
        $book = new Book();
        $book->title = $request->title;
        $book->author = $request->author;
        $book->price = $request->price;
        $book->comment = $request->comment;
        $book->save();

        return redirect("/book");
    }
```

create.blade.phpでは$bookを受け取るようになっています。
そこで、createメソッドでは空のインスタンスを渡すようにします。
画面には空の状態で出力されるようになります。

> 変数を渡さないとUndefined Variableエラーになってしまいます。

以上でCRUD処理全てが実装できました。
ルーティングと流れを意識しながら色々動かして見てください。

## バリデーション

最後にバリデーションを作成しましょう。
バリデーションとは入力内容のチェックのことです。
いくつか方法がありますが、フォームリクエストを作成する方法を紹介します。
フォームリクエストを作成するにはArtisanの`make:request`を使用します。

```bash
php artisan make:request BookRequest
Request created successfully.
```

`app/Http/Requests`ディレクトリが作成され、そこにBookRequest.phpが作成されます。
BookRequest.phpのauthorizeメソッドと、rulesメソッドを以下のように修正します。

> app/Http/Requests/BookRequest.php

```php
<?php

namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

class BookRequest extends FormRequest
{
    /**
     * Determine if the user is authorized to make this request.
     *
     * @return bool
     */
    public function authorize()
    {
        // trueに変更します
        return true;
    }

    public function rules()
    {
        return [
            'title' => 'required|string|max:100',
            'author' => 'required|string|max:50',
            'price' => 'required|integer',
            'comment' => 'nullable',
       ];
    }
}
```

> ※authorizeメソッドのreturnは、忘れずにtrueに変更します。
（falseを返した場合は403ステータスコードのレスポンスが返されます。）


連想配列の値のはバリデーションルールを記述します。

|名前|ルール|
|:--:|:--:|
|required|必須入力|
|string|文字列のみ|
|max:50|50文字以下|
|integer|数値のみ|
|nullable|空もOK|

バリデーションルールの詳細に関しては公式サイトを参考にしてください。
https://readouble.com/laravel/5.7/ja/validation.html

次にBookController.phpのstoreメソッドとupdateメソッドの引数の型指定をRequestからBookRequestに書き換えます。


`use App\Http\Requests\BookRequest;`を忘れないようにしてください。

> app/Http/Contorllers/BookController.php

```php
namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Http\Requests\BookRequest;
use App\Http\Controllers\Controller;
use App\Book;

class BookController extends Controller
{

　〜中略〜

 public function update(BookRequest $request, $id)
    {
        $book = Book::findOrFail($id);
        $book->title = $request->title;
        $book->author = $request->author;
        $book->price = $request->price;
        $book->comment = $request->comment;
        $book->save();

        return redirect("/book");
    }

    public function create()
    {
        $book = new Book();
        return view('book/create', compact('book'));
    }

    public function store(BookRequest $request)
    {
        $book = new Book();
        $book->title = $request->title;
        $book->author = $request->author;
        $book->price = $request->price;
        $book->comment = $request->comment;
        $book->save();

        return redirect("/book");
    }
```

これで書籍名を空にしたまま登録や編集しようとすると、同じ画面にリダイレクトするようになります。
このままでは何が起きているのかわからないので、エラーメッセージを表示するようにしましょう。
`views/book`ディレクトリにmessage.blade.phpを作成します。

```php
<div class="row">
    <div class="col-md-12">
    @if ($errors->any())
        <div class="alert alert-danger">
          <ul>
              @foreach ($errors->all() as $error)
                  <li>{{ $error }}</li>
              @endforeach
          </ul>
        </div>
    @endif
    </div>
</div>
```

これをform.blade.phpに挿入します。

```php
<div class="container ops-main">
    <div class="row">
        <div class="col-md-6">
            <h2>書籍登録</h2>
        </div>
    </div>
    <div class="row">
        <div class="col-md-8 col-md-offset-1">

            @include('book/message')

            <!-- 中略 -->
        </div>
    </div>
</div>
```

これでエラーメッセージが表示されるようになります。

以上でCRUD機能を持つWebアプリケーションが完成しました。




[戻る](/web_application/index.md) /
[トップへ](/README.md)