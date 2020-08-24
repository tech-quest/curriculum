## ルーティングとは
ルーティングとは、クライアントからのリクエストを受け付け、その内容によって処理を振り分けることです。

ルーティング情報は`routes/web.php`ファイルに記述します。

laravel環境でcomposerを構築し終わった状態では、以下のような記載になっています。

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

上記のように記述した場合、「localhost:8000/bookにGETリクエストがきたら、BookControllerのindexメソッドに処理を振り分けて」という意味になります。

## ルートパラメータ
ルートパラメータについても知っておきましょう。

> routes/web.php
```php
<?php
Route::get('book/{id}', 'BookController@show');
```

上記のように記述した場合、「localhost:8000/book/1にGETリクエストがきたら、BookControllerのshowメソッドに処理を振り分けて」という意味になります。
BookControllerのshowメソッドに引数を持たせる事でURLの「1」を受け取れることができます。

> 例）app/Http/Controllers/BookController.php
```php
public function show($id)
{
    return view('book', ['book' => Book::findOrFail($id)]);
}
```

URIに指定された「1」という数字が$idという引数の中に代入されます。


[laravelチュートリアルに戻る](/web_application/laravel_tutorial.md) /
[トップへ](/README.md)