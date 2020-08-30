# djangoチュートリアル #05

データベースとの接続

今回はメジャーな3つのデータベース管理システムであるSqlite3、MySQL、Postgresとの接続方法を説明します。

## 完成版プロジェクト

https://github.com/shun-rec/django-website-05

## 目次

0. 事前準備
1. Sqlite3と接続しよう
2. MySQLと接続しよう
3. Posgresと接続しよう

## 事前準備

Paiza Cloudで以下３つを選択して新規サーバーを作成して下さい 。

* django
* MySQL
* Postgres

## Sqlite3と接続しよう

### 接続設定

実は設定不要！
デフォルトでSqlite3と接続するようになっています。
メインのデータベースとしては力不足。
本番で重要なデータの保存先として使われることはまずありません。
社内限定や内輪限定など使用ユーザーが少ない、かつデータが重要ではないサービスならOK。

### 接続確認

#### プロジェクトのデータベース内にテーブル作成

15行ほどログが出力されて成功すればOKです。

```
python manage.py migrate
```

#### 試しにスーパーユーザー（管理者）を作成してみよう

ユーザー名、メール、パスワードを聞かれるので答えると作成されます。

```
python manage.py createsuperuser
```

`/admin` の管理画面URLから今のユーザーでログイン出来るようになります。
管理画面の詳細はまた後ほど。

## MySQLと接続しよう

### 共通ライブラリのインストール

MySQLとPostgres共通で必要になるライブラリを２つインストールします。

* dj-database-url: データベースの接続設定が１行で楽に書けるライブラリ
* python-dotenv: `.env`という環境設定ファイルを使ってプロジェクトの設定が出来るライブラリ

```sh
pip install dj-database-url
pip install python-dotenv
```

### 全体設定ファイルの編集

もともとあるDATABASESの欄を削除して以下を追記。

```py
import dj_database_url
from dotenv import (
    find_dotenv,
    load_dotenv,
)
load_dotenv(find_dotenv())
DATABASES = {
    'default': dj_database_url.config(conn_max_age=600),
}
```

* load_dotenvで環境設定ファイルを読み込み、
* dj_database_url.configで自動的に設定されます。
* conn_max_age=600というのは今は理解不要ですが、高速化の設定です。

### MySQL用ライブラリのインストール

```
pip install mysqlclient
```

### MySQL上にプロジェクト用データベースを作成

#### MySQLにログイン

`root`というユーザー名でログインするという意味。

```
mysql -u root
```

#### djangoからMySQLに接続するユーザーのパスワードを設定

`root`というユーザーのパスワードを`password`に変更するという意味。

```
ALTER USER 'root'@'localhost' IDENTIFIED BY 'password';
```

#### プロジェクト用のデータベース作成

`pj_db`という新しいデータベースを作成するという意味。

```
create database pj_db;
```

### 環境設定ファイルを設置

プロジェクト直下に`.env`というファイル名でファイルを作り、以下の内容を入力。

※ドットから始まるファイル名は隠しファイルと言って、デフォルトでは非表示です。Paizaではプロジェクトフォルダで右クリックをすると、「隠しファイルを表示」することが出来ます。

`mysql`の`pj_db`というデータベースに`root`ユーザーで`password`パスワードでアクセスするという意味。
```
DATABASE_URL=mysql://root:password@localhost/pj_db
```

### 接続確認

Sqlite3の場合と同様のため省略します。

## Postgresと接続しよう

### 共通ライブラリのインストール

MySQLと同じため省略します。

### Postgres用ライブラリのインストール

```py
pip install psycopg2-binary
```

### プロジェクト用のデータベースを作成

#### Postgresにログイン

```
sudo -u postgres psql postgres
```

#### ユーザーにパスワード設定

`postgres`というユーザーにパスワードを設定するという意味。

```
\password postgres
```

#### データベース作成

```
CREATE DATABASE pj_db;
```

#### Postgresからログアウト

```
\q
```

### 環境設定ファイルを設置

```
DATABASE_URL=postgres://postgres:password@localhost/pj_db
```

### 接続確認

Sqlite3の場合と同様のため省略します。

## Herokuの本番サーバーで接続できるか確認しよう

追加の手順はありません。前回と同様にHerokuサーバーにアップロードするだけです。

〜詳細説明〜

手元のPostgresには当然Herokuからはアクセス出来ません。
実はHerokuにはデフォルトで無料のPostgresがインストールされています。
環境設定ファイルはSettingsタブのConfig Varsから自動で作成されます。

