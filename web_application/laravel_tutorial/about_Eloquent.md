## Eloquent ORM

EloquentはLaravelでデータ操作をするための実装です。
書籍管理アプリでBookモデルというのを作成したのを思い出してください。
BookモデルはBookテーブルにマッピングされており、テーブルの登録や取得更新などの操作を持っています。
Bookモデルとした場合、特に指定しなければ命名規則によりbooksテーブルとマッピングされます。

### レコードの取得
モデルにマッピングされたテーブルの全てのレコードを取得するにはallメソッドを利用します。
tinkerを立ち上げてDB操作してみましょう。


```bash
$ php artisan tinker
```

以下のメッセージが表示され、tinkerでの入力モードになります。

```bash
Psy Shell v0.10.4 (PHP 7.2.26 — cli) by Justin Hileman
```

次に、`use \App\Book;`の入力をします。Bookモデルを利用する、という意味になります。

```bash
>>> use \App\Book;
```

次のコマンドを実行すると、booksテーブルの全データが表示されます。

```bash
>>> $books = Book::all();
```

以下のメッセージが出力されれば成功です。

```bash
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

countメソッドを利用することができます。以下を入力してください、3と表示されればOKです（booksテーブルのレコード件数）

```bash
>>> $books->count();
=> 3
```

### 取得条件の設定

取得レコードに条件を設定する場合はwhereメソッドを使います。
以下を入力してみましょう。

```bash
>>> $expensiveBooks = Book::where('price', '>=', 2500)->get();
```

以下の通り、priceが2500以上のレコードが表示されれば成功です。

```bash
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
```

booksテーブルのidが2のレコードが出力されれば成功です。

```bash
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
ます最初はインスタンスを作成します。

```bash
>>> $newBook = new Book();
```

```bash
=> App\Book {#4029}
```

作成したら、title、author、price、commentを1つずつ以下の通りに入力します。

※ >>> が入力出来るモードで、 => はその結果が表示されます。1つずつ自身で入力して確認しましょう。

```bash
>>> $newBook->title = 'Cake PHP入門';
=> "Cake PHP入門"
>>> $newBook->author = '神奈川　健太郎';
=> "神奈川　健太郎"
>>> $newBook->price = 1980;
=> 1980
>>> $newBook->comment = 'Cake PHPの学習ならこれがおすすめ！'
=> "Cake PHPの学習ならこれがおすすめ！"
```

booksの上記4つの項目の入力が完了したら、以下のコマンドで保存します。
なおidを入力しないのは、idの項目の定義で、自動で番号を採番してくれるからです。

```bash
>>> $newBook->save();
=> true
```

先程入力した内容が保存されたかを確認しましょう。
以下を入力します。

```bash
>>> $cakePhpBook = Book::find(4);
```

以下が表示されれば成功です。

```bash
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

以下を入力します、つい先程入力した`Book::find(4)`の情報が格納されています。

```bash
>>> $cakePhpBook;
```

```bash
=> App\Book {#4030
     id: 4,
     title: "Cake PHP入門",
     author: "神奈川　健太郎",
     price: 1980,
     comment: "Cake PHPの学習ならこれがおすすめ！",
     created_at: "2020-08-05 05:29:46",
     updated_at: "2020-08-05 05:29:46",
   }
```

著者名を変更しましょう。以下の通り入力します。

```bash
>>> $cakePhpBook->author = '埼玉 五郎';
=> "埼玉 五郎"
```

saveメソッドで、保存します。

```bash
>>> $cakePhpBook->save();
=> true
```

それでは改めてfindメソッドで、著者名が変更されたかを確認します。

```bash
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

更新の確認ができましたか？

Eloquentでは、複数件の更新も可能です。
以下のように、priceが3000以上だったら、priceを3300に変更する事も出来ます。

```bash
>>> Book::where('price', '>=', 3000)->update(['price' => 3300]);
=> 2
```

実行が成功すると、更新したレコード件数が表示されます。今回は2件ですから、2が表示されます。

`Book::all();`で、結果を確認します。

```bash
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
```

以下のdeleteメソッドを実行しましょう。

```bash
>>> $cakePhpBook->delete();
=> true
```

実行に成功するとtrueとなります。最後に、findで検索を行い、消されたことを確認します（nullと表示されればOK）

```bash
>>> $cakePhpBook->find(4);
=> null
>>>
```

tinkerを終了する際は、以下のコマンドで終了しましょう。

```bash
>>> exit;
```

以上がEloquentの説明でした。


[laravelチュートリアルに戻る](/web_application/laravel_tutorial.md) /
[トップへ](/README.md)