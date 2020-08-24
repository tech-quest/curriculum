## Rollbackについて

マイグレーションを取り消したい場合はmigrate:rollbackコマンド、またはmigrate:resetコマンドを使用します。
migrate:rollbackコマンドは直前のマイグレーションのみ取り消します。

```bash
% php artisan migrate:rollback
```

以下のようなメッセージが表示されれば成功です。

```bash
Rolling back: 2018_10_30_085034_create_books_table
Rolled back:  2018_10_30_085034_create_books_table
```

migrate:resetは全てのマイグレーションを取り消します。

```bash
% php artisan migrate:reset
```
以下のようなメッセージが表示されれば成功です。
```bash
Rolling back: 2018_10_30_085034_create_books_table
Rolled back:  2018_10_30_085034_create_books_table
```

[laravelチュートリアルに戻る](/web_application/laravel_tutorial.md) /
[トップへ](/README.md)