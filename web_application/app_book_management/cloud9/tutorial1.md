# 書籍管理アプリ(cloud9)

## 目次
[1.環境構築](#1-環境構築)

[2.DBの準備](#2-DBの準備)

[3.書籍一覧画面の作成](#3-書籍一覧画面の作成)
 
[4.書籍登録画面の作成](#4-書籍登録画面の作成)
 
[5.書籍編集画面の作成](#5-書籍編集画面の作成)

## 1. 環境構築

### 1.PHPのバージョン確認

 `$ php -v`

下記のように表示されPHPのバージョンが5.6であることが確認できます。

```
PHP 5.6.38 (cli) (built: Oct 16 2018 23:34:40) 
Copyright (c) 1997-2016 The PHP Group
Zend Engine v2.6.0, Copyright (c) 1998-2016 Zend Technologies
    with Xdebug v2.5.5, Copyright (c) 2002-2017, by Derick Rethans
```

### 2.MySQLのバージョン確認

`$ mysql --version`

下記のように表示されMySQLのバージョンが5.5であることが確認できます。

```
mysql  Ver 14.14 Distrib 5.5.61, for Linux (x86_64) using readline 5.1
```

### 3.インストール

`$ sh -c "$(curl -fsSL https://gist.githubusercontent.com/MisterTeacher/e680ee45d468aa5e33c7f3d14175a1ca/raw/7d5ea114daf01b2ed1aa5ee62427f60574359976/cloud9-laravel-installer.sh)" `

### 4.再度、バージョン確認(PHP)

`$ php -v`

PHPのバージョンを確認すると7.2になっていることを確認できます。

```
PHP 7.2.11 (cli) (built: Oct 16 2018 23:50:44) ( NTS )
Copyright (c) 1997-2018 The PHP Group
Zend Engine v3.2.0, Copyright (c) 1998-2018 Zend Technologies
    with Zend OPcache v7.2.11, Copyright (c) 1999-2018, by Zend Technologies 
```

### 5.再度、バージョン確認(MySQL)

`$ mysql --version`

MySQLのバージョンを確認すると5.7になっていることを確認できます。

```
mysql  Ver 14.14 Distrib 5.7.23, for Linux (x86_64) using  EditLine wrapper
以上で環境構築は完了です。万が一失敗したらCloud9のワークスペースを削除して再度ワークスペースを作成したあとに手順を最初からやり直してみてください。
```

### 6.Laravelインストール

`$ composer create-project laravel/laravel sample`

### 7.作成したディレクトリ移動

`$ cd sample`

### 8.画面表示できるかの確認

Webサーバーの起動

`$ php artisan serve --port=8080`

previewをクリック(preview Running Application)

## 2. DBの準備

### 1.DBとテーブルの作成

①MySQL を止める

`$ sudo service mysqld stop`

②MySQL を起動する

`$ sudo service mysqld start`

③MySQL にログインする

`$ mysql -u root -p`

パスワードを求められるかと思いますが、空白で Enter 押しちゃってください。

④データベースを作成する

`$ create database laravel;`

今回は laravel という名前のデータベースを作成しました。

⑤.作成したデータベースを確認する

`$ show databases;`

⑥作成したデータベースを選択する

`$ use laravel`

⑦データベーステーブルを確認する

`$ show tables;`

```
Empty set (0.00 sec)
```

とありますのでまだテーブルはないことが確認できます。
ここからデータベースマイグレーションでテーブルを作成していきますので MySQL からログアウトします。

⑧MySQL からログアウト

`$ exit;`

### 2.マイグレーション

①booksテーブルを作成

`$ php artisan make:migration create_books_table --create=books`

②booksテーブルのupメソッドの修正

[database/migrations/yyyy_mm_dd_hhssmm_create_books_table.php]

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateBooksTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('books', function (Blueprint $table) {
            /*
            *  以下のようにテーブルに持たせる項目を書きます。
            */
            $table->increments('id');
            $table->string('title', 100);
            $table->string('author', 50);
            $table->integer('price');
            $table->text('comment')->nullable();
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

③マイグレーションの実行

`$ php artisan migrate`

### 3.マイグレーションができたかの確認

①MySQL にログインする

`$ mysql -u root -p`

パスワードを求められるかと思いますが、空白で Enter 押しちゃってください。

②laravelデータベースを選択する

`mysql>  use laravel;`

③データベーステーブルを確認する

`mysql> show tables;`

④booksテーブルが指定通り作成されているか確認

`mysql> DESC books;`

⑤MySQL からログアウト

`$ exit;`

## 3. 書籍一覧画面の作成

### 1.Modelの作成

`$ php artisan make:model Book`

### 2.Modelが作成されたか確認

App/Book.phpディレクトリがあるか確認

### 3.Viewの作成

①resources/views/ディレクトリの中にbookディレクトリを作成

②resources/views/bookディレクトリの中にindex.blade.phpを作成

③index.blade.phpに下記のコードをコピペ

[resources/views/book/index.blade.php]

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
			  <th class="text-center">コメント</th>
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

### 4.Controllerの作成

`$ php artisan make:controller BookController`

### 5.Controllerが作成されたか確認

app/Http/Controllers/BookController.phpがあるか確認

### 6.BookCotrollerにindexメソッドを追加

[app/Http/Controllers/BookController.php]

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

}
```

### 7.Routingの設定

web.phpファイルを編集

[routes/web.php]

```php
<?php

use Illuminate\Support\Facades\Route;

/*
|--------------------------------------------------------------------------
| Web Routes
|--------------------------------------------------------------------------
|
| Here is where you can register web routes for your application. These
| routes are loaded by the RouteServiceProvider within a group which
| contains the "web" middleware group. Now create something great!
|
*/

Route::resource('book', 'BookController');
```

### 8.一覧画面が表示されるか確認

/book にアクセスしてみましょう。 
> 一覧画面
![一覧画面](../img/check_index_action.png)

## 4. 書籍登録画面の作成

### 1.create.blade.phpの作成

[resources/views/book/create.blade.php]

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
            <form action="/book" method="post">
                @csrf
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

### 2.BookControllerの設定

[app/Http/Controllers/BookController.php]

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
        // DBよりBookテーブルの値をすべて取得
        $books = Book::all();

        // 取得した値をビュー「book/index」に渡す
        return view('book/index', compact('books'));
    }

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
}
```

### 3.登録画面が表示されるか確認	

/book/create にアクセスしてみましょう。

> 登録画面
![check_create_action](../img/check_create_action.png)

## 5. 書籍編集画面の作成

### 1.edit.blade.phpの作成

[resources/views/book/edit.blade.php]

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

### 2.Routingの設定

[routes/web.php]

app/Http/Controllers/BookController.php

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
        // DBよりBookテーブルの値をすべて取得
        $books = Book::all();

        // 取得した値をビュー「book/index」に渡す
        return view('book/index', compact('books'));
    }

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
    
    public function edit($id)
    {
        // DBよりURIパラメータと同じIDを持つBookの情報を取得
        $book = Book::findOrFail($id);

        // 取得した値をビュー「book/edit」に渡す
        return view('book/edit', compact('book'));
    }

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

    public function destroy($id)
    {
        $book = Book::findOrFail($id);
        $book->delete();

        return redirect("/book");
    }

}
```

### 3.編集画面が表示されるか確認	

/book/edit にアクセスしてみましょう。

> 編集画面
![check_edit_action](../img/check_edit_action.png)

### 4.機能の動作確認

#### ①新規登録機能の動作確認

①_1.まずは一覧画面に遷移します。

http://localhost:8000/book

①_2.画面下の新規作成ボタンをクリック
> 一覧画面
![check_index_action](../img/check_index_action.png)

①_3.書籍登録の画面で4つの項目を入力し、登録ボタンをクリック
> 登録画面
![check_create_action](../img/check_create_action.png)

```
書籍名　→　7日でわかるPHP入門！
著者名　→　神奈川 裕次郎
価格　　→　4000
コメント →　あっという間にPHPが理解できます！
```

①_4.一覧画面に表示されたか確認

> 一覧画面
![check_store_action](../img/check_store_action.png)


#### ②編集機能の動作確認

②_1.まずは一覧画面に遷移します。

http://localhost:8000/book

②_2.ID1をクリック

> 一覧画面
![一覧画面](../img/check_store_action.png)

②_3.書籍編集の画面で4つの項目を修正し、登録ボタンをクリック

> 編集画面
![check_edit_action](../img/check_edit_action.png)

```
書籍名　→　2週間でわかるPHP入門！
著書名　→　千葉 三郎
価格　　→　3500
コメント →　たったの2週間でPHPが理解できます！
```

②_4.一覧画面に表示されたか確認

> 一覧画面
![check_up_action](../img/check_up_action.png)

#### ③削除機能の動作確認

③_1.まずは一覧画面に遷移します。

http://localhost:8000/book

③_2.一覧画面の右端のゴミ箱のアイコンをクリック

> 一覧画面
![一覧画面](../img/check_store_action.png)

③_3.一覧画面からID1の書籍情報が消えている事を確認

> 一覧画面
![check_index_action](../img/check_index_action.png)

[戻る](/web_application/index.md) /
[トップへ](/README.md)