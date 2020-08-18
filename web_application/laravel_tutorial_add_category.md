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
|JavaによるWebアプリケーション作成入門|Java|
|Cake PHP入門|PHP|
|Pyhtonによるデータ分析入門|Python|
|はじめてのPyhton入門|Python|

上表のように、書籍名：PHPBoookは、`カテゴリ：PHP`に区分けされます。書籍名：Laravel Bookも`カテゴリ:PHP`に区分けされます。書籍名：JavaによるWebアプリケーション作成入門は、`カテゴリ：Java`に区分します。
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

    ・Pythonというあるカテゴリに、2つ（複数）の書籍（Book）が属する

のように、2つ区分けされています。カテゴリPHPは、

    ・PHPというカテゴリに、3つ（複数）の書籍（Book）が属する

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


## シーディング（テストデータ）の作成

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

Categoriesテーブルに、以下のレコードを登録します。

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

なお、BooksTableSeederを読み込む設定は一旦コメントアウトすることを忘れずに行いましょう。

> database/seeds/DatabaseSeeder.php
```php
public function run()
{
     // BooksTableSeederを読み込むように指定
     // コメントアウトします
     // $this->call(BooksTableSeeder::class);

     // CategoriesTableSeederを読み込むように指定
     $this->call(CategoriesTableSeeder::class);
}
```

## シーディングの実行
シーディングを実行するにはArtisanの`db:seed`コマンドを実行します。

```bash
  php artisan db:seed

  以下のメッセージが表示されたら成功です。

  Seeding: CategoriesTableSeeder
  Seeded:  CategoriesTableSeeder (0.2 seconds)
  Database seeding completed successfully.
```



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
冒頭の「リレーションシップ（1対多）とは」で、カテゴリと書籍の関係は、1対多となるとお話しましたが、その定義をモデルに記載します。
これを行えば、例えば,

`「カテゴリ名がPythonの書籍を、すべて検索したい」`

といった機能を実現出来ます。まずは実際にコードを書きましょう。

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

のため、多となるBookを複数形としてメソッド名とします。

次にメソッドの中は、

    return $this->hasMany('App\Book');

と定義します。`$thisは疑似変数`といいますが、ここでは`モデルCategoryクラス自身`のことを指します。

次のhasManyメソッドが1対多を実現するためのメソッドで、第一引数に（'App\Book'）と定義しています。これはモデルBookの相対パスです（Appディレクトリ直下にBookモデルがある）。

これにより、1対多を実現しています。理解出来ましたでしょうか？
もし難しければ以下のような英文で覚えてみましょう。

    Category has Many books. （カテゴリは、多くの書籍を持つ）
    → Category（自モデル名、$this）、has Many （hasManyメソッド）、books（メソッド名とhasManyの第一引数）

このようにして「カテゴリは、多くの書籍を持つ」というデータ構造を実現することが出来ます。このようなhasManyなどの2つのテーブル間のリレーションシップ定義は他にもありますので、インターネットでぜひ検索してみましょう。

## カテゴリコントローラーの作成

カテゴリのテーブルとモデルの準備が整いましたので、次はコントローラーの作成に移ります。
以下のコマンドで、コントローラーを作成してください。

```bash
  php artisan make:controller CategoryController

  以下のメッセージが表示されたら成功です。


  以下のメッセージが表示されたら成功です。
  Controller created successfully.
```



以下のようにカテゴリコントローラーが作成されたかを確認します。

  > app/Http/Controllers/CategoryController.php

  以下のメッセージが表示されたら成功です。


    ```php

    以下のメッセージが表示されたら成功です。



      <?php

      以下のメッセージが表示されたら成功です。




    namespace App\Http\Controllers;

use Illuminate\Http\Request;



class CategoryController extends Controller
{
    //
}
```

一旦、ルーティングの設定に移ります。

## ルーティングの設定

カテゴリを画面から登録出来るようにするためには、CRUDを制御して画面との橋渡しを担うコントローラーが必要になります、Bookと同様に以下のようにweb.phpにルーティング定義を追加します。

> routes/web.php

```php
    Route::resource('book', 'BookController');
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
