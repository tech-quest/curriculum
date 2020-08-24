# 書籍管理アプリケーション 機能改修（カテゴリ編）
## このチュートリアルの目的

laravelのチュートリアルで作成した、書籍管理アプリケーションで実装したBookに対して、`Category（カテゴリ）`を追加する対応を行いましょう。
この対応を経験すると、以下のことを新しく学習できますので、ぜひチャレンジしてください！

1. 既に作成したテーブルに対して、項目を追加する方法が経験出来る
1. 2つのテーブル間の関連性について、理解と必要な作業の経験が出来る（ここでは`1対多`のリレーションシップを経験）

## カテゴリとは

前回のチュートリアルでは、書籍の情報を管理するBook（テーブル名books）を作成しました。
本は、書店などに行けば、技術書、経済書、スポーツ誌、マンガ、などのように、本がどの区分なのかを管理・整理している事がほとんどです。このチュートリアルでは、その本がどんなカテゴリに属するのかを管理したいと思います。
以下のように、各本をそれぞれのカテゴリに登録することとします。

|書籍名|カテゴリ|
|:--:|:--:|
|PHP Book|PHP|
|Laravel Book|PHP|
|日本一わかりやすいPHPとLaravel|PHP|
|JavaによるWebアプリケーション作成入門|Java|
|Pyhtonによるデータ分析入門|Python|
|はじめてのPyhton入門|Python|

上表のように、書籍名：PHPBoookは、`カテゴリ：PHP`に区分けされます。

書籍名：Laravel Bookも`カテゴリ:PHP`に区分けされます。

書籍名：JavaによるWebアプリケーション作成入門は、`カテゴリ：Java`に区分します。


このように、書籍は何かしらの区分（カテゴリ）に仕分けされるようにします。
こうすると、同じカテゴリごとに検索したいなどの要望にも対応が出来ます。

## リレーションシップ（1対多）とは
先程の表では、

    ・ カテゴリ：PHPには3つの書籍
    ・ カテゴリ：Javaには1つの書籍
    ・ カテゴリ：Pythonには2つの書籍

が区分けされています。例えばカテゴリのJavaは、

    ・Javaというある1つのカテゴリに、1つの書籍（Book）が属する

が区分けされています。カテゴリPythonは、

    ・Pythonというある1つのカテゴリに、2つ（複数）の書籍（Book）が属する

のように、2つ区分けされています。カテゴリPHPは、

    ・PHPというある１つのカテゴリに、3つ（複数）の書籍（Book）が属する

のように、3つ区分けされています。

今回の例では書籍数が少ないですが、`ある1つのカテゴリには、様々な複数の書籍が属する`ことになります。

これを言い換えると、`1対多の関係性（リレーションシップ）`といいます。

今回は、categoriesテーブルで管理されているカテゴリが「1」となり、booksテーブルで管理されている本が「多」となる、
ということを覚えてください。

Laravelでこの1対多のリレーションシップの実現は、`Eloquent`の機能を用いれば実現出来ますので、ぜひ活用していきましょう。

## カテゴリ機能の作成

それでは、上記のPHP/Java/Pythonのようなカテゴリ自体を管理するテーブルと、モデル、コントローラーをそれぞれ順番に作成していきます。

## カテゴリテーブルの作成

カテゴリテーブル(categories)のテーブルを作成するマイグレーションファイルを作成します。
以下のコマンドを実行してください。
`テーブル名は必ず複数形`とすることを忘れないようにしましょう！

```bash
　・ php artisan make:migration create_categories_table --create=categories

以下のメッセージが出力されれば成功です。
    Created Migration: 2020_08_17_070604_create_categories_table
```

以下のファイルが作成されます。

> database/migrations/2020_08_17_070604_create_categories_table.php

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateCategoriesTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('categories', function (Blueprint $table) {
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
        Schema::dropIfExists('categories');
    }
}
```

それではここでカテゴリテーブルにはどんな項目を保持するかを考えてみます。
先程のプログラムの書籍達に、PHP/Java/Python、のようにカテゴリの名前をデータとして持てば良いと判断出来ますので、2020_08_17_070604_create_categories_table.phpのupメソッドを以下のように修正します。

> 2020_08_17_070604_create_categories_table.php
```php
    public function up()
    {
        Schema::create('cagtegories', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->timestamps();
        });
    }
```

nameという項目名として新しく追加し、型をstringとします。
これで、カテゴリ名を保存するテーブルを作成する準備が整いました。
以下のコマンドを実行し、テーブルを作成しましょう。

```bash
　・ php artisan migrate

以下のメッセージが出力されれば成功です。
Migrating: 2020_08_17_070604_create_categories_table
Migrated:  2020_08_17_070604_create_categories_table (0.09 seconds)
```

mysqlにも接続して、テーブル名「categories」が作成されたことを確認します。

```sql
mysql> show tables;

+------------------+
| Tables_in_sample |
+------------------+
| books            |
| categories      |
| failed_jobs      |
| migrations       |
| users            |
+------------------+
5 rows in set (0.00 sec)
```

以下コマンドで、カテゴリテーブルの項目にid、name、created_at、updated_atの項目が作成されているかを確認します。

```sql
mysql> show columns from categories;
+------------+---------------------+------+-----+---------+----------------+
| Field      | Type                | Null | Key | Default | Extra          |
+------------+---------------------+------+-----+---------+----------------+
| id         | bigint(20) unsigned | NO   | PRI | NULL    | auto_increment |
| name       | varchar(255)        | NO   |     | NULL    |                |
| created_at | timestamp           | YES  |     | NULL    |                |
| updated_at | timestamp           | YES  |     | NULL    |                |
+------------+---------------------+------+-----+---------+----------------+
```

以上が、カテゴリテーブル（categories）を作成する手順です。


### シーディング（テストデータ）の作成

作成したカテゴリテーブルに、テストデータを登録しましょう。

### シーディングファイルの作成
以下のArtisanのコマンドを実行します。

```bash
  ・ php artisan make:seeder CategoriesTableSeeder

  以下のメッセージが出力されれば成功です。
  Seeder created successfully.
```

`database/seeds`ディレクトリにCategoriesTableSeeder.phpが作成されます。
> database/seeds/CategoriesTableSeeder.php
```php
<?php

use Illuminate\Database\Seeder;

class CategoriesTableSeeder extends Seeder
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

categoriesテーブルに、以下のレコードを登録します。

**categoriesテーブル**

|id|name|created_at|updated_at|
|:--:|:--:|:--:|:--:|
|1|PHP|現在時刻|現在時刻|
|2|Python|現在時刻|現在時刻|
|3|Java|現在時刻|現在時刻|

上記レコードを登録するため、シーダーファイルを以下の通りに修正します。

> database/seeds/CategoriesTableSeeder.php

```php
<?php

use Illuminate\Database\Seeder;

class CategoriesTableSeeder extends Seeder
{
    /**
     * Run the database seeds.
     *
     * @return void
     */
    public function run()
    {
        // categoriesテーブルのデータをクリアする
        DB::table('categories')->truncate();

        // 初期データを用意する（列名をキーとする連想配列で用意する）
        $categories = [
            [
                'name' => 'PHP',
            ],
            [
                'name' => 'Python',
            ],
            [
                'name' => 'Java',
            ]
        ];

        // テストデータを登録する
        foreach ($categories as $category) {
            \App\Category::create($category);
        }
    }
}
```

さらにDatabaseSeeder.phpのrunメソッドを以下のように修正します。runメソッド内でcallしたクラスが、シーディングコマンドで実行されるようになります。

なお、BooksTableSeederを読み込む設定はそのままにしましょう。
既に作成済みのBooksテーブル用のシーディングで、データを再度作るようにします。

> database/seeds/DatabaseSeeder.php
```php
public function run()
{
     // BooksTableSeederを読み込むように指定
     $this->call(BooksTableSeeder::class);

     // CategoriesTableSeederを読み込むように指定
     $this->call(CategoriesTableSeeder::class);
}
```

### シーディングの実行
シーディングを実行するにはArtisanの`db:seed`コマンドを実行します。

```bash
  php artisan db:seed

  以下のメッセージが表示されたら成功です。

  Seeding: BooksTableSeeder
  Seeded:  BooksTableSeeder (0.19 seconds)
  Seeding: CategoriesTableSeeder
  Seeded:  CategoriesTableSeeder (0.07 seconds)
  Database seeding completed successfully.
```

実行が完了したら、SQLでテーブルデータが保存をされたかを確認しましょう。
まず、categoriesテーブルを確認します。select文を実行し、以下のようにデータが格納されていれば成功です。

```sql
select * from categories;
+----+--------+---------------------+---------------------+
| id | name   | created_at          | updated_at          |
+----+--------+---------------------+---------------------+
|  1 | PHP    | 2020-08-18 01:58:04 | 2020-08-18 01:58:04 |
|  2 | Python | 2020-08-18 01:58:04 | 2020-08-18 01:58:04 |
|  3 | Java   | 2020-08-18 01:58:04 | 2020-08-18 01:58:04 |
+----+--------+---------------------+---------------------+
3 rows in set (0.00 sec)
```

booksテーブルも確認しましょう、select文を実行し、以下のようにデータが格納されていれば成功です。

```sql
mysql> select * from books;
+----+------------------------------------------+---------------+-------+---------------------------------------+---------------------+---------------------+
| id | title                                    | author        | price | comment                               | created_at          | updated_at          |
+----+------------------------------------------+---------------+-------+---------------------------------------+---------------------+---------------------+
|  1 | PHP Book                                 | 山田 太郎     |  2000 | PHPで一番オススメ                     | 2020-08-18 01:58:04 | 2020-08-18 01:58:04 |
|  2 | Laravel Book                             | 東京 次郎     |  3000 | Laravel本で一番わかりやすい           | 2020-08-18 01:58:04 | 2020-08-18 01:58:04 |
|  3 | 日本一わかりやすいPHPとLaravel           | Tech Quest社  |  3500 | 日本で一番売れている本                | 2020-08-18 01:58:04 | 2020-08-18 01:58:04 |
+----+------------------------------------------+---------------+-------+---------------------------------------+---------------------+---------------------+
3 rows in set (0.00 sec)
```

以上で、シーディングによるデータに準備が整いました。

## カテゴリモデルの作成

次に、カテゴリモデル（Category）を作成しましょう。
以下のコマンドを実行します。カテゴリ名は`単数形`です！

```bash
  ・  php artisan make:model Category

  以下のメッセージが出力されれば成功です。
  Model created successfully.
```

以下ディレクトリに、Category.phpが作成されたことを確認します。

> app/Category.php
```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Category extends Model
{
    //
}
```

これでモデルCategoryが作成されたことになりますが、ここで行わなければならない事があります。それは、CategoryとBookとの2つのテーブル間にリレーション関係の定義する必要があります。
冒頭の「リレーションシップ（1対多）とは」で、カテゴリと書籍の関係は、`1対多`となるとお話しましたが、その定義をモデルに記載します。
これを行えば、例えば,

`「カテゴリ名がPythonの書籍を、すべて検索したい」`

といった機能を実現出来ます。

それでは1対多を実現するコードを書いていきましょう。

> app/Category.php
```php
class Category extends Model
{
    public function books()
    {
        return $this->hasMany('App\Book');
    }
}
```

モデルCategoryクラスに、booksメソッドを定義します。
これは今回の場合、

    Category : Book = 1 : 多

のため、多となるBookを複数形としてメソッド名`books()`とします。

次にメソッドの中は、

    return $this->hasMany('App\Book');

と定義します。`$thisは疑似変数`といいますが、ここでは`モデルCategoryクラス自身`のことを指します。

次のhasManyメソッドが1対多を実現するためのメソッドで、第一引数に（'App\Book'）と定義しています。これはモデルBookの相対パスです（Appディレクトリ直下にBookモデルがある）。

これにより、1対多を実現しています。理解出来ましたでしょうか？
もし難しければ以下のような英文で覚えてみましょう。

    Category has many books. （カテゴリは、多くの書籍を持つ）
    → Category（自モデル名、$this）、has many （hasManyメソッド）、books（メソッド名とhasManyの第一引数）

このようにして「カテゴリは、多くの書籍を持つ」というデータ構造を実現することが出来ます。このようなhasManyなどの2つのテーブル間のリレーションシップ定義は他にもありますので、インターネットでぜひ検索してみましょう。

## カテゴリコントローラーの作成

カテゴリのテーブルとモデルの準備が整いましたので、次はコントローラーの作成に移ります。
以下のコマンドで、コントローラーを作成してください。

```bash
  ・　php artisan make:controller CategoryController

  以下のメッセージが表示されたら成功です。

  Controller created successfully.
```

以下にカテゴリコントローラーが作成されたかを確認します。

  > app/Http/Controllers/CategoryController.php

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class CategoryController extends Controller
{
    //
}
```

ここで一旦、ルーティングの設定に移ります。

## ルーティングの設定

カテゴリを画面から登録、編集、削除が出来るようにするためには、先程作成したコントローラーで、CRUDをコントロールしますが、ルーティングの設定も必要になります。Bookと同様に以下のようにweb.phpにルーティング定義を追加します。

> routes/web.php

```php
    Route::resource('book', 'BookController');
    // 以下のルーティング設定を追加します。
    Route::resource('category', 'CategoryController');
```

一旦定義が正しく追加されたかを以下のコマンドで確認しましょう。

```bash
php artisan route:list

+--------+-----------+--------------------------+------------------+-------------------------------------------------+------------+
| Domain | Method    | URI                      | Name             | Action                                          | Middleware |
+--------+-----------+--------------------------+------------------+-------------------------------------------------+------------+
|        | GET|HEAD  | api/user                 |                  | Closure                                         | api        |
|        |           |                          |                  |                                                 | auth:api   |
|        | GET|HEAD  | book                     | book.index       | App\Http\Controllers\BookController@index       | web        |
|        | POST      | book                     | book.store       | App\Http\Controllers\BookController@store       | web        |
|        | GET|HEAD  | book/create              | book.create      | App\Http\Controllers\BookController@create      | web        |
|        | GET|HEAD  | book/{book}              | book.show        | App\Http\Controllers\BookController@show        | web        |
|        | PUT|PATCH | book/{book}              | book.update      | App\Http\Controllers\BookController@update      | web        |
|        | DELETE    | book/{book}              | book.destroy     | App\Http\Controllers\BookController@destroy     | web        |
|        | GET|HEAD  | book/{book}/edit         | book.edit        | App\Http\Controllers\BookController@edit        | web        |
|        | GET|HEAD  | category                 | category.index   | App\Http\Controllers\CategoryController@index   | web        |
|        | POST      | category                 | category.store   | App\Http\Controllers\CategoryController@store   | web        |
|        | GET|HEAD  | category/create          | category.create  | App\Http\Controllers\CategoryController@create  | web        |
|        | GET|HEAD  | category/{category}      | category.show    | App\Http\Controllers\CategoryController@show    | web        |
|        | PUT|PATCH | category/{category}      | category.update  | App\Http\Controllers\CategoryController@update  | web        |
|        | DELETE    | category/{category}      | category.destroy | App\Http\Controllers\CategoryController@destroy | web        |
|        | GET|HEAD  | category/{category}/edit | category.edit    | App\Http\Controllers\CategoryController@edit    | web        |
+--------+-----------+--------------------------+------------------+-------------------------------------------------+------------+

```

categoryのルーティング設定が追加されたことを確認します。
ルーティングの詳細は、「Laravel入門　使い方チュートリアル」を参照下さい。

## カテゴリ一覧の作成
それでは、再びカテゴリコントローラー(CategoryController.php)に戻ります。
まずは、カテゴリの一覧画面を作成し、シーディングで登録したデータが、画面上で表示されるかを確認します。

まず、CategoryController.phpを以下のように作成します。

 1. カテゴリデータを、categoriesテーブルから取得
 1. 取得したデータを、category/index.blade.phpに、カテゴリデータを渡し、表示する

という処理を実装します。

> app/Http/Controllers/CategoryController.php

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Category;

class CategoryController extends Controller
{
    public function index(){

        // DBよりCategoryテーブルの値を全て取得
        $categories = Category::all();

        // 取得した値をビュー「category/index」に渡す
        return view('category/index', compact('categories'));
    }
}
```

作成が完了したら、viewの作成に移ります。

### カテゴリ一覧ビューの作成

それではindex.blade.phpを作成していきますが、書籍管理のBookを参考にし、各画面で共通的に利用するbladeファイルもこのタイミングで作成します。まずは、layout.blade.phpから作成しましょう。

1. resources/viewsフォルダの下に、フォルダ名：category、を作成します。
1. 作成した後、categoryフォルダの中に、ファイル：layout.blade.phpを、を作成します。以下の通りに作成してください。

> resources/views/category/layout.blade.php

```php
<head>
    <title>Category</title>
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css">
</head>
@yield('content')
```

次に、同じく`resources/views/category/`の直下に、index.blade.phpを作成しましょう。

> resources/views/category/index.blade.php

```php
@extends('category/layout')
@section('content')
<div class="container ops-main">
    <div class="row">
        <div class="col-md-12">
            <h3 class="ops-title">カテゴリ一覧</h3>
        </div>
    </div>
    <div class="row">
        <div class="col-md-11 col-md-offset-1">
            <table class="table text-center">
                <tr>
                    <th class="text-center">ID</th>
                    <th class="text-center">タグ名</th>
                    <th class="text-center">削除</th>
                </tr>
                @foreach($categories as $category)
                <tr>
                    <td>
                        <a href="/category/{{ $category->id }}/edit">{{ $category->id }}</a>
                    </td>
                    <td>{{ $category->name }}</td>
                    <td>
                        <form action="/category/{{ $category->id }}" method="post">
                            <input type="hidden" name="_method" value="DELETE">
                            <input type="hidden" name="_token" value="{{ csrf_token() }}">
                            <button type="submit" class="btn btn-xs btn-danger" aria-label="Left Align"><span
                                    class="glyphicon glyphicon-trash"></span></button>
                        </form>
                    </td>
                </tr>
                @endforeach
            </table>
            <div><a href="/category/create" class="btn btn-default">新規作成</a></div>
        </div>
    </div>
</div>
@endsection
```

これで、カテゴリ一覧を表示させる準備が出来ました。
ここでサーバーを起動して、カテゴリ一覧が表示されるかを確認します。
以下のコマンドを実行し、サーバーを起動します。

```bash
php artisan serve --host=localhost --port=8000

以下が表示されれば成功です。
Laravel development server started: http://localhost:8000
```

サーバーが起動出来たら、お使いのパソコンのブラウザ（Google Chrome等)のアドレス欄に、以下のアドレスをコピーしてアクセスしましょう。

http://localhost:8000/category

上記URLにアクセスすると、シーディングで登録したカテゴリのデータが一覧として表示された事がわかるはずです。
サーバーは、利用が終わったら停止しましょう。
ターミナル上で、「Control + C」を押せばサーバーは停止します。

### カテゴリの新規登録機能の作成

カテゴリを新たに登録するための機能を作成しましょう。
まずは、CategoryControllerに、
 1. 新しくカテゴリを登録する画面に遷移させるためのcreateメソッド
 1. 上記で入力したカテゴリ情報をデータベースへ保存する処理を記載したstoreメソッド

の2つを追加しましょう。
先程CategoryController.phpにて追加した、indexメソッドの下に追加します。

> app/Http/Controllers/CategoryController.php
```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Category;

class CategoryController extends Controller
{
    public function index()
    {
        // DBよりCategoryテーブルの値を全て取得
        $categories = Category::all();

        // 取得した値をビュー「category/index」に渡す
        return view('category/index', compact('categories'));
    }

    public function create()
    {
        // 新規画面と編集画面で共通したbladeファイルを用意するため、空のCategoryインスタンスをbladeへ渡す。
        // blade側にcategoryを渡さないとエラーとなるための処置
        $category = new Category();
        return view('category/create', compact('category'));
    }

    public function store(Request $request)
    {
        $category = new Category();
        $category->name = $request->name;
        // categoriesテーブルにデータを新たに登録します
        $category->save();
        return redirect("/category");
    }
}
```

新規登録画面を作成します、書籍管理と同じく、新規画面と後ほど作成する編集画面はほぼ同じ画面構成となりますので、予め両方で利用ができるようにbladeファイルを作成しましょう。
`form.blade.php`をcategoryフォルダの配下に作成します。

> resources/views/category/form.blade.php

```php
<div class="container ops-main">
    <div class="row">
        <div class="col-md-6">
            <h2> タグ登録</h2>
        </div>
    </div>
    <div class="row">
        <div class="col-md-8 col-md-offset-1">
            @if($target == 'store')
            <form action="/category" method="post">
                @elseif($target == 'update')
                <form action="/category/{{ $category-id }}" method="post">
                    <!-- updateメソッドにはPUTメソッドがルーティングされているので、PUTにする -->
                    <input type="hidden" name="_method" value="PUT">
                    @endif
                    <input type="hidden" name="_token" value="{{ csrf_token() }}">
                    <div class="form-group">
                        <label for="title">タグ名</label>
                        <input type="text" class="form-control" name="title" value="{{ $category->name }}">
                    </div>
                    <button type="submit" class="btn btn-default">登録</button>
                </form>
        </div>
    </div>
</div>
```

categoryフォルダの配下に、新規作成画面の`create.blade.php`を作成します。

>resources/views/category/create.blade.php
```php
@extends('category/layout')
@section('content')
@include('category/form', ['target' => 'store'])
@endsection
```

create.blade.phpの@includeで、form.blade.phpを読み込みます。
また変数targetにstoreをセットする事で、新規作成の場合と編集の場合でformタグが分岐条件によって変化するようになります。storeの場合は新規作成のformへと切り替わります。

これで、カテゴリ新規登録画面の完成です。

サーバーを起動して、新規登録が動作するかを確認しましょう。
以下のコマンドを実行し、サーバーを起動します。

```bash
php artisan serve --host=localhost --port=8000

以下が表示されれば成功です。
Laravel development server started: http://localhost:8000
```

サーバーが起動出来たら、お使いのパソコンのブラウザ（Google Chrome等)のアドレス欄に、以下のアドレスをコピーしてアクセスしましょう。

http://localhost:8000/category

上記URLにアクセスすると、カテゴリのデータが一覧表示されます。
画面下部に「新規登録」ボタンがありますので、クリックしましょう。新規登録画面に遷移します。

カテゴリ名に「C言語」と入力して、「登録」ボタンをクリックしましょう。

登録が成功すると、一覧画面に遷移し、4つ目のカテゴリとして、「C言語」が登録出来た事が確認できると思います。

サーバーは、利用が終わったら停止しましょう。
ターミナル上で、「Control + C」を押せばサーバーは停止します。

以上で、新規登録画面の実装が完了しました。

### カテゴリの編集機能の作成

カテゴリの名称を変更したい場合の、編集画面を作成します。
CategoryControllerに、
 1. カテゴリを編集する画面に遷移させるためのeditメソッド
 1. 上記で入力したカテゴリ情報をデータベースへ保存する処理を記載したupdateメソッド

の2つを追加しましょう。
先程CategoryController.phpにて追加した、storeメソッドの下に追加します。

> app/Http/Controllers/CategoryController.php

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Category;

class CategoryController extends Controller
{
    public function index()
    {
        // DBよりCategoryテーブルの値を全て取得
        $categories = Category::all();

        // 取得した値をビュー「category/index」に渡す
        return view('category/index', compact('categories'));
    }

    public function create()
    {
        // 新規画面と編集画面で共通したbladeファイルを用意するため、空のCategoryインスタンスをbladeへ渡す。
        // blade側にcategoryを渡さないとエラーとなるための処置
        $category = new Category();
        return view('category/create', compact('category'));
    }

    public function store(Request $request)
    {
        $category = new Category();
        $category->name = $request->name;
        $category->save();
        return redirect("/category");
    }

    public function edit($id)
    {
        // DBからURIパラメータと同じIDを持つCategoryの情報を取得
        $category = Category::findOrFail($id);

        // 取得した値をビュー「category/edit」に渡す
        return view('category/edit', compact('category'));
    }

    public function update(Request $request, $id)
    {
        $category = Category::findOrFail($id);
        $category->name = $request->name;
        // カテゴリ名を変更して、DBへ保存します
        $category->save();
        return redirect('/category');
    }
}
```

次に編集画面を作成します。編集画面の入力は、新規登録画面と共通していることは先程案内した通りですので、編集画面もform.blade.phpを読み込むようにします。
resource/categoryの配下に「edit.blade.php」を作成しましょう。

>　resources/views/category/edit.blade.php
```php
@extends('category/layout')
@section('content')
@include('category/form', ['target' => 'update'])
@endsection
```

これで編集画面の作成は完了です（新規登録画面で多めに作っておいてよかったですね！）
では、再びサーバーを起動しましょう。

以下のコマンドを実行し、サーバーを起動します。

```bash
php artisan serve --host=localhost --port=8000

以下が表示されれば成功です。
Laravel development server started: http://localhost:8000
```

サーバーが起動出来たら、お使いのパソコンのブラウザ（Google Chrome等)のアドレス欄に、以下のアドレスをコピーしてアクセスしましょう。

http://localhost:8000/category

上記URLにアクセスすると、カテゴリのデータが一覧表示されます。
項目のIDがリンクになっています。先程C言語で登録しましたが、そのIDをクリックしましょう。

編集画面に遷移します。カテゴリ名を「C言語」から「C++言語」へと名称を変更して、「登録」ボタンをクリックしましょう。

登録が成功すると、一覧画面に遷移し、カテゴリの名称が、「C++言語」へと変更が出来た事が確認できると思います。

サーバーは、利用が終わったら停止しましょう。
ターミナル上で、「Control + C」を押せばサーバーは停止します。

以上で、変更画面の実装が完了しました。


### カテゴリの削除機能

カテゴリの削除機能を作成しましょう。
カテゴリの一覧画面にすでに削除ボタンは作成しているので、CategoryControllerにdestroyメソッドを定義します。

> app/Http/Controllers/BookController.php

```php
public function destroy($id)
{
    $book = Book::findOrFail($id);
    $book->delete();

    return redirect("/book");
}
```

では再びサーバーを起動しましょう。サーバーの起動コマンドは新規と編集の確認で行った時と同様ですので、ぜひ覚えてくださいね！

起動したら、カテゴリの一覧画面に遷移しましょう。

http://localhost:8000/category

上記URLにアクセスすると、カテゴリのデータが一覧表示されます。
各カテゴリの最後にゴミ箱のマークがあると思います。そのボタンを押すと、そのカテゴリは削除されますので、先程変更した「C++言語」を削除してみましょう。
「C++言語」の削除ボタンをクリックします。

削除が成功すると、再びカテゴリ一覧へと戻り、「C++言語」が削除されていると思います。

以上で、削除処理の実装が完了しました。

### バリデーション

最後にバリデーション（入力内容のチェック）を追加しましょう。
カテゴリを登録・編集を行う時に、カテゴリ名の記入が無い場合はエラーとするようにしていきます。
フォームリクエストを作成は、書籍と同じくArtisanの`make:request`を使用します。

```bash
php artisan make:request CategoryRequest

以下のように表示されたら成功です。
Request created successfully.
```

`app/Http/Requests`ディレクトリにCategoryRequest.phpが作成されます。
CategoryRequest.phpのauthorizeメソッドと、rulesメソッドを以下のように修正します。

> app/Http/Requests/CategoryRequest.php
```php
<?php

namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

class CategoryRequest extends FormRequest
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

    /**
     * Get the validation rules that apply to the request.
     *
     * @return array
     */
    public function rules()
    {
        // return内部の連想配列に、キー名name、値にルールを記載
        return [
            'name' => 'required|string|max:50',
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

バリデーションルールの詳細に関しては公式サイトを参考にしてください。
https://readouble.com/laravel/5.7/ja/validation.html

次に`CategoryController.php`のstoreメソッドとupdateメソッドの引数の型指定をRequestからCategoryRequestに書き換えます。

また、use句に、`use App\Http\Requests\CategoryRequest;`を忘れないように定義してください。

> app/Http/Controllers/CategoryController.php
```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Category;
/*
 *  以下のuseを忘れずに記載しましょう
 */
use App\Http\Requests\CategoryRequest;

class CategoryController extends Controller
{
    public function index()
    {
        // DBよりCategoryテーブルの値を全て取得
        $categories = Category::all();

        // 取得した値をビュー「category/index」に渡す
        return view('category/index', compact('categories'));
    }

    public function create()
    {
        // 新規画面と編集画面で共通したbladeファイルを用意するため、空のCategoryインスタンスをbladeへ渡す。
        // blade側にcategoryを渡さないとエラーとなるための処置
        $category = new Category();
        return view('category/create', compact('category'));
    }

    // Request $requestを、CategoryRequest $requestに変更しましょう
    public function store(CategoryRequest $request)
    {
        $category = new Category();
        $category->name = $request->name;
        $category->save();
        return redirect("/category");
    }

    public function edit($id)
    {
        // DBからURIパラメータと同じIDを持つCategoryの情報を取得
        $category = Category::findOrFail($id);

        // 取得した値をビュー「category/edit」に渡す
        return view('category/edit', compact('category'));
    }

    // Request $requestを、CategoryRequest $requestに変更しましょう
    public function update(CategoryRequest $request, $id)
    {
        $category = Category::findOrFail($id);
        $category->name = $request->name;
        $category->save();
        return redirect("/category");
    }

    public function destroy($id)
    {
        $category = Category::findOrFail($id);
        $category->delete();
        return redirect('/category');
    }
}
```

これでカテゴリ名を空にしたまま登録や編集しようとすると、エラーとなるのですが、同じ画面にリダイレクトするようになります。
このままでは何が起きているのかわからないので、エラーメッセージを表示するようにしましょう。
`views/category`ディレクトリにmessage.blade.phpを作成します。

> resources/views/category/message.blade.php

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

このmessage.blade.phpを、form.blade.phpに組み込みます。

> resources/views/category/form.blade.php

```php
<div class="container ops-main">
    <div class="row">
        <div class="col-md-6">
            <h2>カテゴリ登録</h2>
        </div>
    </div>
    <div class="row">
        <div class="col-md-8 col-md-offset-1">
            @include('category/message')
            @if($target == 'store')
            <form action="/category" method="post">
                @elseif($target == 'update')
                <form action="/category/{{ $category->id }}" method="post">
                    <!-- updateメソッドにはPUTメソッドがルーティングされているので、PUTにする -->
                    <input type="hidden" name="_method" value="PUT">
                    @endif
                    <input type="hidden" name="_token" value="{{ csrf_token() }}">
                    <div class="form-group">
                        <label for="name">カテゴリ名</label>
                        <input type="text" class="form-control" name="name" value="{{ $category->name }}">
                    </div>
                    <button type="submit" class="btn btn-default">登録</button>
                </form>
        </div>
    </div>
</div>
```

これでエラーメッセージが表示されるようになりますので、サーバーを起動して確認しましょう。
新規画面で、カテゴリ名に名前を入力せずに登録ボタンを押すと、エラーメッセージが表示される事が確認できると思います。

以上で、カテゴリのCRUDが完成しました。

さて次からはいよいよ、`カテゴリを書籍に紐付けする対応を行っていきます！`

## 書籍にカテゴリを追加する

それではカテゴリの準備が整いましたので、書籍を登録・編集する時にカテゴリを選択するように修正しましょう。

例えば、

    ・ 書籍名：「PHP Book」のカテゴリはPHP
    ・ 書籍名:「日本一わかりやすいPHPとLaravel」のカテゴリはPHP

のように、書籍には必ずカテゴリを選ぶ（保持）するようにします。
つまり、書籍（booksテーブル）に、カテゴリのデータを保持するようにします。
まず、booksテーブルの構造を思い出してみましょう。

**booksテーブル**

|id|title|author|price|comment|created_at|updated_at|
|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
|1|PHP Book|山田 太郎|2000|PHP本で一番おすすめ|現在時刻|現在時刻|
|2|Laravel Book|東京 次郎|3000|Laravel本で一番わかりやすい|現在時刻|現在時刻|
|3|日本一わかりやすいPHPとLaravel|Tech Quest社|3500|日本で一番売れている本|現在時刻|現在時刻|


booksテーブルは書籍の情報を格納するテーブルでした。このテーブルに、カテゴリの情報を保持することになります。改めてカテゴリテーブルのデータを確認しましょう。

**catgoriesテーブル**

|id|name|created_at|updated_at|
|:--:|:--:|:--:|:--:|
|1|PHP|現在時刻|現在時刻|
|2|Python|現在時刻|現在時刻|
|3|Java|現在時刻|現在時刻|

現在のbooksテーブルの書籍の3つすべてが`カテゴリ：PHP`となります。
booksテーブルにカテゴリを保持する上で、categoriesテーブルには、

    ・id
    ・name
の2つの項目がありますが、どちらをbooksテーブルに持てばいいでしょうか？

正解は、`id`です。2つのテーブル間の関係性を紐付けるときは、idを使うようにしましょう。booksテーブルにcategory_idを追加するイメージは以下の通りです。
idの後ろに付与してみましょう。

**booksテーブル**

|id|category_id|title|author|price|comment|created_at|updated_at|
|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
|1|1|PHP Book|山田 太郎|2000|PHP本で一番おすすめ|現在時刻|現在時刻|
|2|1|Laravel Book|東京 次郎|3000|Laravel本で一番わかりやすい|現在時刻|現在時刻|
|3|1|日本一わかりやすいPHPとLaravel|Tech Quest社|3500|日本で一番売れている本|現在時刻|現在時刻|

もし、カテゴリ名がPHPから仮にPHP5.6などのように変更されたとしても、booksテーブルではcategory_idを持っていますので、カテゴリ名の名称の変更の影響をbooksテーブルが受けないなどのメリットがあります。

なお、このようにbooksテーブルはcategory_idという`外部キー`を持つということになりますので、ぜひ覚えてください。
外部キーの命名は、参照先のテーブルを単数形とした名称とする事も覚えてください。

それでは実際にbooksテーブルに、category_idの外部キーを追加してみましょう。

*** booksテーブルにcategory_idを追加

テーブルに項目を追加するときは、以下のコマンドを実行します。

```bash
php artisan make:migration add_category_id_books_table

以下のメッセージが出力されれば成功です。
Created Migration: 2020_08_19_022930_add_category_id_books_table
```

実行すると、database/migrationsの配下に、マイグレーションファイルが出来ますので確認しましょう。

> database/migrations/2020_08_19_022930_add_category_id_books_table.php

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class AddCategoryIdBooksTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        //
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        //
    }
}
```

AddCategoryIdBooksTableクラスというphpファイルが作成されました。
中身を確認すると、まだupメソッドとdownメソッドの中身がありませんので、こちらに具体的にcategory_idを追加するプログラムを記載していきます。

> database/migrations/2020_08_19_022930_add_category_id_books_table.php

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class AddCategoryIdBooksTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::table('books', function (Blueprint $table) {
            $table->BigInteger('category_id')->after('id');
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::table('books', function (Blueprint $table) {
            $table->dropColumn('category_id');
        });
    }
}
```

upメソッドの注目は、
```php
$table->BigInteger('category_id')->after('id');
```
です。BigIntegerメソッドは項目の型ですが、categoriesのidがBigInteger型ですので、型をあわせる必要があります。category_idという名前で、BigInteger型で作成する、という意味になります。

次の`after('id')`は、booksテーブルのidのすぐ後ろに、category_idを追加するという動作となります。

一方のdownメソッドは、category_idを削除する場合の定義となります。