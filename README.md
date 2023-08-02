# workshop-backend-template
バックエンドアプリケーション開発向けのワークショップ環境です。

## 目次

- [はじめに](#はじめに)
- [ワークショップ](#ワークショップ)


## はじめに

「🤖」は、Dev Containersの仕組みを使って自動で実行されるタスクです。

<details>
<summary><b>【1】SSH接続に必要な公開鍵と秘密鍵の準備</b></summary>

このワークショップでは、リモートマシンに接続して作業を行います。\
リモートマシンに接続する際に必要な情報は、進行担当者からお知らせします。

SSH接続に必要な秘密鍵は通常`~/.ssh/`などに格納して管理しますがワークショップでの一時利用のため、任意のディレクトリに格納してください。SSHのコンフィグファイル(`~/.ssh/config`)を作成する際に保存した場所のディレクトリパスを使用します。

**補足**

```
【SSH接続時に使用する公開鍵と秘密鍵について】
通常であれば初回のみパスワード認証によるSSH接続を行ってから`~/.ssh/authorized_keys`に公開鍵を登録しますが、ワークショップの時間短縮のため運営側で機械的に作成して登録したものをお渡しします。
```

</details>

<details>
<summary><b>【2】リモートマシンに接続</b></summary>

</details>

<details>
<summary><b>【3】Dev Containersの起動</b></summary>

</details>

<details>
<summary>【4】PythonとPostgreSQLの実行環境(🤖)</summary>

このワークショップでは、PythonとPostgreSQLの実行環境が必要ですが、Dockerの仕組みを使ってあらかじめDockerfileとdocker-compose.ymlを用意しています。\
Dev Containersでは、このDockerfileとdocker-compose.ymlを使って環境を構築します。
</details>

<details>
<summary>【5】Pythonパッケージのインストール(🤖)</summary>

[poetry](https://github.com/python-poetry/poetry)を使ってパッケージをインストールします。\
通常は`poetry install`コマンドを実行して依存パッケージをインストールしますが、Dev Containersの仕組みを使って自動でインストールされるようにしています。
</details>

<details>
<summary>【6】DBの初期構築(🤖)</summary>

マイグレーションツールの1つである[alembic](https://github.com/sqlalchemy/alembic)を使ってデータベースの初期構築を行います。\
通常は`alembic upgrade head`コマンドを実行してデータベースを作成しますが、Dev Containersの仕組みを使って自動で作成されるようにしています。
</details>

<details>
<summary><b>【7】Gitのユーザ名とメールアドレスを設定</b></summary>

GitHubを使用する際は、Gitの設定でユーザ名とメールアドレスを設定する必要があります。\
普段使用しているものがあればそれを設定してください。初めての方は次のように設定してください。

| 項目 | 設定内容 |
| --- | --- |
| `<ユーザ名>` | GitHub IDを入力 |
| `<メールアドレス>` | GitHubに登録しているメールアドレスを入力 |

※もし入力する内容に抵抗がある場合は、進行担当者にお知らせください。

```shell
git config --local user.name "<ユーザ名>"
git config --local user.email "<メールアドレス>"
```

**タスク機能を使用して設定する場合**

Command + Pを押して、`Tasks: Run Task`を選択してください。\
`Git: Set git config user.name and user.email`を選択してください。\
画面の指示に従ってユーザ名とメールアドレスを入力してください。
</details>

<details>
<summary><b>【8】アプリケーションの起動</b></summary>

このワークショップでは、よく使うコマンドをMakefileに集約しています。\
アプリケーションを起動する際は、makeコマンドを使用してください。

```shell
make start
```

次のように`Application startup complete.`と表示されたら、`https://localhost:8000/docs`にアクセスしてAPIのドキュメントが表示されることを確認してください。

```shell
INFO:     Will watch for changes in these directories: ['/workspaces/workshop-backend-template']
INFO:     Uvicorn running on http://127.0.0.1:8000 (Press CTRL+C to quit)
INFO:     Started reloader process [12433] using WatchFiles
INFO:     Started server process [12440]
INFO:     Waiting for application startup.
INFO:     Application startup complete.
```
</details>

## ワークショップ

参加者は、【1】のみを実施してください。\
後ほど講師役の方が【2】を実施してから参加者の方にプルリクエストを投げます。

<details>
<summary><b>【1】送料を追加(参加者が実施)</b></summary>

送料を追加するために幾つかのファイルを編集していきます。

**モデル**

このワークショップではORMを使ってデータベースを操作します。\
モデルは、データベースのテーブルと対応しています。

まずはじめにモデルに送料を表すフィールドを追加します。
追加方法は、[item.py](api/models/item.py)に以下のコードを追記してください。

```diff
class Item(Base):
    __tablename__ = "items"

    id = Column(Integer, primary_key=True)
    name = Column(String(1024))
    price = Column(Integer)
+   shipping_fee = Column(Integer)
```

**スキーマ**

スキーマにも同じように送料を追加します。\
スキーマは、APIのリクエストやレスポンスの形式を定義しています。

[item.py](api/schemas/item.py)に以下のコードを追記してください。

```diff
class ItemBase(BaseModel):
    name: str = Field(..., example="一眼レフカメラ", description="商品名")
    price: int = Field(..., example=1000, description="価格")
+   shipping_fee: int = Field(..., example=500, description="送料")
```
</details>

<details>
<summary>【2】手数料を追加(講師役が実施)</summary>

手数料を追加するために幾つかのファイルを編集していきます。\
送料と同じようにモデルとスキーマに追加します。

**モデル**

[item.py](api/models/item.py)に以下のコードを追記してください。

```diff
class Item(Base):
    __tablename__ = "items"

    id = Column(Integer, primary_key=True)
    name = Column(String(1024))
    price = Column(Integer)
    shipping_fee = Column(Integer)
+   commission = Column(Integer)
```

**スキーマ**

[item.py](api/schemas/item.py)に以下のコードを追記してください。

```diff
class ItemBase(BaseModel):
    name: str = Field(..., example="一眼レフカメラ", description="商品名")
    price: int = Field(..., example=1000, description="価格")
    shipping_fee: int = Field(..., example=500, description="送料")
+   commission: int = Field(..., example=100, description="手数料")
```
</details>
