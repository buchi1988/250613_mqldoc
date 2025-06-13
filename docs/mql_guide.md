# 概要

ENOVIA Matrix Query Language (MQL) は、MatrixOne/ENOVIA 環境のビジネスオブジェクトを操作するためのコマンドライン言語です。データ取得からオブジェクト作成、権限管理までさまざまな操作を行えます。

# 基本構文と実行方法

MQL は `mql` コマンドで起動し、`MQL>` プロンプト上で命令を実行します。終了するには `quit` を入力します。

```text
$ mql
MQL> login user "engineer" password "pass"
MQL> help
MQL> quit
```

単一行実行の場合はコマンドライン引数に指定することも可能です。

```text
$ mql -c "print person 'engineer' select email"
```

# ビジネスオブジェクト操作

製品や部品などのビジネスオブジェクトの作成、検索、更新例を示します。

```text
MQL> add type Part
MQL> add businessobject Part "P-0001" revision "A" policy "Development"
MQL> print businessobject Part "P-0001" select name revision current
MQL> modify businessobject Part "P-0001" description "試作部品"
MQL> delete businessobject Part "P-0001"
```

# リレーション操作

オブジェクト同士の関係付けや解除には `connect`、`disconnect` コマンドを用います。

```text
MQL> connect businessobject Part "P-0001" relationship "Bill Of Material" to businessobject Part "P-0002"
MQL> print connection "Bill Of Material" select from.name to.name
MQL> disconnect businessobject Part "P-0001" relationship "Bill Of Material" to businessobject Part "P-0002"
```

# 管理オブジェクト

タイプやポリシー、属性など管理オブジェクトの設定例です。

```text
MQL> add attribute Attr_Weight type real
MQL> add type Part derivedfrom Generic attributes Attr_Weight
MQL> print type Part select name derived
MQL> modify type Part add attribute Attr_Color string
```

# クエリ／レポート

複雑な検索や一覧取得を行う例をいくつか示します。

```text
MQL> temp query bus Part * * where "attribute[Attr_Weight] > 10" select name revision attribute[Attr_Weight]
MQL> query connection type "Bill Of Material" where "from.name == 'P-0001'"
MQL> list person select name email
```

# スクリプト化と TCL 連携

MQL はスクリプトファイルとして保存することもでき、TCL から呼び出すことで自動化が可能です。

```text
MQL> source $HOME/scripts/create_parts.mql
```

TCL からの例：

```tcl
set result [exec mql -c "print bus Part P-0001 A select current"]
puts $result
```

# ベストプラクティス

- 重要な操作の前に `print` で確認する
- スクリプトには適切なコメントを残す
- `temp query` を使いすぎないようパフォーマンスを意識する

# トラブルシューティング

よくある問題と対処例です。

```text
MQL> print bus Part "P-0001" A select name current
// 結果が返らない場合：オブジェクト名・リビジョンを再確認

MQL> login
// エラー: "Invalid Password" => パスワード入力ミスを確認
```

# チートシート

- `add businessobject <type> <name> revision <rev>`: オブジェクト作成
- `modify businessobject <type> <name>`: 属性変更
- `connect businessobject <from> relationship <rel> to <to>`: リレーション設定
- `temp query bus <type> <name> <rev>`: 一時検索
- `history <object>`: 履歴表示

# 参考文献／リンク

- Dassault Systèmes 公式ドキュメント
- 社内手順書やチーム Wiki

# 更新履歴

| 日付 | 版数 | 内容 |
| --- | --- | --- |
| 2025-06-13 | 1.0 | 初版 |
