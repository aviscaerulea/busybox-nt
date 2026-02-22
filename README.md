# busybox-nt

Windows 向けに UTF-8 対応・速度最適化した [busybox-w32](https://github.com/rmyorston/busybox-w32) のビルドシステム。

## 概要

[busybox-w32](https://github.com/rmyorston/busybox-w32) は BusyBox の Windows ネイティブ移植版で、Windows 上で `ls`, `grep`, `awk` などの Unix コマンドを単一の実行ファイルとして提供する。

本プロジェクトは以下の設定でビルドした `busybox.exe` を提供する。

| 設定 | 内容 |
|---|---|
| ターゲット | x64 (x86_64-w64-mingw32) |
| defconfig | `mingw64u_defconfig` |
| 最適化 | `-O2`（速度優先） |
| Unicode 対応 | UTF-8 マニフェスト、入出力、幅処理（CJK 全角・結合文字）|

## 必要なツールキット

WSL2 (Ubuntu 推奨) 上で以下のツールが必要。

```bash
sudo apt install gcc-mingw-w64-x86-64 make git zip
```

| ツール | インストール方法 |
|---|---|
| WSL2 (Ubuntu) | Microsoft Store / `wsl --install` |
| MinGW-w64 クロスコンパイラ | `sudo apt install gcc-mingw-w64-x86-64` |
| GNU Make | `sudo apt install make` |
| git | `sudo apt install git` |
| [go-task](https://taskfile.dev/) v3 | `sudo sh -c "$(curl --location https://taskfile.dev/install.sh)" -- -d -b /usr/local/bin` |
| zip | `sudo apt install zip` |

## 使い方

```bash
# ソース取得（最新 FRP タグをチェックアウト）
task clone

# ビルド
task build

# リリース用 zip 作成（build を自動実行）
task release

# ビルド成果物の削除
task clean
```

## 自動ビルド

GitHub Actions による自動ビルドが設定されている。

| 項目 | 内容 |
|---|---|
| 自動実行 | 毎日定時に上流リポジトリの新タグを検知し、新バージョンがあれば自動でビルド・リリース |
| 手動実行 | GitHub Actions の「Run workflow」ボタンから手動トリガー可能 |
| 成果物 | [Releases](https://github.com/aviscaerulea/busybox-nt/releases) ページで公開 |

## ディレクトリ構成

```
busybox-nt/
├── .gitignore
├── Taskfile.yml
├── README.md
└── out/                    ← task release で生成
    ├── busybox.exe
    └── busybox-*_x64.zip
```

ビルド作業領域は WSL ネイティブファイルシステム上に配置される（Windows マウント経由の I/O 低速化を回避するため）。

```
/tmp/busybox-w32/           ← task clone で取得（WSL ネイティブFS）
/tmp/busybox-nt-build/      ← task build の中間ファイル（WSL ネイティブFS）
```

> **注意**: WSL2 の再起動で `/tmp` はクリアされる。`task clone` を再実行すれば復元可能。

## ライセンス

- ビルドシステム（本リポジトリ）: MIT
- busybox-w32 本体: GPL-2.0 ([詳細](https://github.com/rmyorston/busybox-w32/blob/master/LICENSE))
