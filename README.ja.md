
# Hikari-Cursor — Windows のマウスカーソルを macOS で

[English](README.md) | [简体中文](README.zh-CN.md) | **日本語**

Windows の `.cur` / `.ani` カーソルテーマを macOS でそのまま使えます。アニメーションカーソルにも対応し、システム全体(ブラウザや Electron を含むすべてのアプリ)に反映されます。macOS 26 (Tahoe) で開発・動作確認済み！

## インストール(一般ユーザー向け)

[Releases](../../releases) から最新の `Hikari-Cursor.dmg` をダウンロードして開き、**Hikari-Cursor** のアイコンを **アプリケーション** フォルダにドラッグするだけです。

初回起動は macOS にブロックされます(Apple の公証は受けておらず、ローカル署名のみのため)。いずれかの方法で許可してください:

- **推奨**:Finder →「アプリケーション」で **Hikari-Cursor を右クリック →「開く」**、ダイアログでもう一度「開く」をクリック。または「システム設定 → プライバシーとセキュリティ」の下部で「このまま開く」をクリック。
- あるいはターミナルで一度実行:`xattr -dr com.apple.quarantine /Applications/Hikari-Cursor.app`

macOS 15 以降が必要です(macOS 26 で開発・確認)。

## ソースからビルド

Xcode / Swift 6 ツールチェーンが必要です。`./build_app.sh` を実行すると、成果物は `dist/` に生成されます:

| 成果物 | 説明 |
|---|---|
| `Hikari-Cursor.app` | GUI + メニューバー常駐アプリ:テーマフォルダを選択 → プレビュー → 個別に割り当て → ワンクリックで適用/復元 |
| `mousecur` | 同機能の CLI。ログイン時の自動適用にも対応(CLI は元の名前を維持し、GUI のブランド名とは独立) |

## 手動マッピング

テーマフォルダを選ぶと、各行の右側にドロップダウンが表示され、そのファイルが macOS のどのカーソルを置き換えるかを指定できます(各項目には macOS カーソルのプレビューアイコンが付き、「よく使う / サイズ変更 / ウィンドウ端・角 / その他」に分類されます)。既定ではファイル名から自動判定され(「自動 · xxx」と表示)、さらに:

- 任意の macOS カーソルに変更可能 —— 自動判定で「対応なし」となったファイル(手書き、個人選択など)も強制的に割り当てられます。
- 「マッピングしない」を選んでファイルをスキップできます。

補助表示:同名ファイル(配色ごとに `busy.ani` を持つパックなど)は所属サブフォルダで区別されます。複数のチェック済みファイルが同じ macOS カーソルに割り当てられると、その行にオレンジの警告が表示されます(実際には最後に適用されたものだけが有効)。手動マッピングは「適用」で保存され、次回起動時に復元されます。

## メニューバー常駐

`Hikari-Cursor.app` は純粋なメニューバーアプリです(`LSUIElement`、Dock アイコンなし)。メインウィンドウを閉じても終了しません —— 右上のメニューバーには常に矢印アイコンがあります。クリックすると:

- Hikari-Cursor を開く…(メインウィンドウを再表示)
- 前回のテーマを再適用 / システム既定に戻す(ウィンドウを開かず操作可能)
- ログイン時に自動適用(チェックボックス。LaunchAgent の導入状況を反映)
- Hikari-Cursor を終了(プロセスを完全に終了し、メニューバーアイコンが消えます)

## CLI クイックスタート

```bash
# テーマ一式を適用(ファイル名から役割を自動判定し macOS スロットへマッピング)
./dist/mousecur apply <テーマフォルダ>

# macOS の既定カーソルに戻す
./dist/mousecur reset

# カーソルサイズ(pt、既定 32)
./dist/mousecur apply <テーマフォルダ> --size 40

# 単一ファイルを特定スロットに適用
./dist/mousecur apply arrow.cur --slot arrow

# カーソルの変更はログアウトで失われます。ログイン時の自動再適用を導入:
./dist/mousecur agent install     # 解除: agent uninstall

# その他
./dist/mousecur info <ファイル>    # フレーム数 / サイズ / ホットスポット
./dist/mousecur preview <ファイル> # フレームを PNG として書き出し
./dist/mousecur slots             # 置き換え可能なシステムカーソルスロット一覧
./dist/mousecur doctor            # プライベート API の利用可否をチェック
```

## 対応ファイル形式

- 静的 `.cur`(BMP 1/4/8/24/32 ビット + AND マスク、または埋め込み PNG)
- アニメーション `.ani`(RIFF/ACON、`rate` / `seq` チャンク・8 ビットパレットフレーム対応)
- **`.cur` に偽装した ANI**(一部のパックは RIFF アニメを `.cur` と命名。内容で自動判別)

## Windows の役割 → macOS スロット対応

| Windows ファイル | macOS カーソル |
|---|---|
| Normal | 矢印(Tahoe の ArrowS、ブラウザ cursor.0 を含む) |
| Text | I ビーム(IBeamS、cursor.1 を含む) |
| Link | 指差しハンド(cursor.13 / PointingHand) |
| Busy | 待機スピナー(Wait) |
| Working | 操作可能なビジー(cursor.4) |
| Unavailable | 禁止(cursor.3 / NotAllowed) |
| Precision | 十字線 |
| Help | ヘルプ |
| Move | 移動 |
| Vertical / Horizontal / Diagonal | 各方向のサイズ変更 + ウィンドウ端/角 |
| Handwriting、Person、Pin、Alternate など | macOS に対応なし、スキップ |

## 注意事項

- カーソルの登録は現在のログインセッション内のみ有効で、ログアウト/再起動で元に戻ります。常駐させるには `mousecur agent install` を使用してください。
- 「既定に戻す」で一部のカーソルがすぐに戻らない場合は、ログアウトして再ログインすれば元に戻ります(登録はもともとセッションをまたぎません)。
- プライベートシステム API を使用しています。メジャーアップデート後に動かなくなったら、まず `mousecur doctor` を実行してください。
- バイナリは個人利用向けのアドホック署名です。再配布には再署名が必要です。

## ライセンス

[MIT](LICENSE) © 2026 zhy072
