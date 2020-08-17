# mbstringを使う（yumでインストール）
それぞれのリンクをクリックするときは、別タブで開く癖をつけましょう。

「⌘ + クリック」(Mac) /「Ctrl + クリック」(Windows)


## 目次
1. バージョン確認
1. リスト
1. インストール
1. 再起動


## ①バージョン確認
```bash
[root@ip-xxx-xx-xx-xx php.d]# php -v
```
```bash
PHP 7.0.16 (cli) (built: Mar  6 2017 19:45:42) ( NTS )
Copyright (c) 1997-2017 The PHP Group
Zend Engine v3.0.0, Copyright (c) 1998-2017 Zend Technologies
```

## ②リスト
```bash
[root@ip-xxx-xx-xx-xx php.d]# yum list | grep "\-mbstring"
```
```bash
php-mbstring.x86_64                     5.3.29-1.8.amzn1               amzn-main
php54-mbstring.x86_64                   5.4.45-1.75.amzn1              amzn-main
php55-mbstring.x86_64                   5.5.38-2.119.amzn1             amzn-main
php56-mbstring.x86_64                   5.6.30-1.133.amzn1             amzn-main
php70-mbstring.x86_64                   7.0.16-1.21.amzn1              amzn-main
```

## ③インストール
```bash
[root@ip-xxx-xx-xx-xx php.d]# yum install php70-mbstring.x86_64
```
```bash
読み込んだプラグイン:priorities, update-motd, upgrade-helper
1004 packages excluded due to repository priority protections
依存性の解決をしています
--> トランザクションの確認を実行しています。
---> パッケージ php70-mbstring.x86_64 0:7.0.16-1.21.amzn1 を インストール
--> 依存性解決を終了しました。

依存性を解決しました

==================================================================================================================
 Package                      アーキテクチャー     バージョン                       リポジトリー             容量
==================================================================================================================
インストール中:
 php70-mbstring               x86_64               7.0.16-1.21.amzn1                amzn-main               1.3 M

トランザクションの要約
==================================================================================================================
インストール  1 パッケージ

総ダウンロード容量: 1.3 M
インストール容量: 2.7 M
Is this ok [y/d/N]: y
Downloading packages:
php70-mbstring-7.0.16-1.21.amzn1.x86_64.rpm                                                | 1.3 MB  00:00:00     
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  インストール中          : php70-mbstring-7.0.16-1.21.amzn1.x86_64                                           1/1 
  検証中                  : php70-mbstring-7.0.16-1.21.amzn1.x86_64                                           1/1 

インストール:
  php70-mbstring.x86_64 0:7.0.16-1.21.amzn1                                                                       
```
## ④再起動



[戻る](/web_application/index.md) /
[トップへ](/README.md)