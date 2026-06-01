<div id="top"></div>

# kicad_my_library

KiCad で使用する自作シンボルおよび自作フットプリントを管理するためのライブラリである．
複数の KiCad プロジェクトで共通して使用する部品データを Git で管理し，PC 移行や環境変更時にも同じライブラリを再利用できるようにすることを目的としている．

## 使用技術一覧

<p style="display: inline">
  <img src="https://img.shields.io/badge/-KiCad-314CB0.svg?logo=kicad&style=for-the-badge&logoColor=white">
</p>

## 目次

1. [概要](#概要)
2. [管理対象](#管理対象)
3. [ディレクトリ構成](#ディレクトリ構成)
4. [KiCadへの登録方法](#kicadへの登録方法)
5. [GitHubへのアップロード方法](#githubへのアップロード方法)
6. [運用方法](#運用方法)
7. [設計上の工夫](#設計上の工夫)

## 概要

本リポジトリは，KiCad で使用する自作部品ライブラリを管理するためのものである．
主に，センサ基板や小型デバイスの設計で使用する独自フットプリントやシンボルを対象とする．

KiCad のプロジェクトごとにフットプリントやシンボルをコピーすると，ライブラリの重複やパスの不整合が発生しやすい．
そこで，本リポジトリではライブラリを一元管理し，各 KiCad プロジェクトから共通ライブラリとして参照する構成をとる．

<p align="right">(<a href="#top">トップへ</a>)</p>

---

## 管理対象

本リポジトリでは，以下のデータを管理する．

* 自作フットプリント
* 自作シンボル

一方で，以下は管理対象外とする．

* KiCad プロジェクト本体
* KiCad プラグイン
* 3Dモデル
* ガーバーデータ
* 一時ファイル
* 自動生成ファイル

<p align="right">(<a href="#top">トップへ</a>)</p>

---

## ディレクトリ構成

```text
.
├── footprints
│   └── MyLibrary.pretty
│       ├── C0603.kicad_mod
│       ├── FPC-SMD_AFC07-S05FCC-00.kicad_mod
│       ├── MICROPARK_L1.6-W1.6-P0.50-BL-1.kicad_mod
│       ├── MLX90632.kicad_mod
│       └── SOT-23-5_L2.9-W1.6-P0.95-LS2.8-BL.kicad_mod
├── symbols
│   └── MyLibrary.kicad_sym
├── .gitignore
└── README.md
```

`footprints` には KiCad のフットプリントライブラリを配置する．
KiCad では，`.pretty` フォルダがフットプリントライブラリであり，各 `.kicad_mod` ファイルが個別のフットプリントに対応する．

`symbols` には KiCad のシンボルライブラリを配置する．
`.kicad_sym` ファイルには，回路図で使用する自作シンボルをまとめて管理する．

<p align="right">(<a href="#top">トップへ</a>)</p>

---

## KiCadへの登録方法

### 1．リポジトリの配置

任意の場所に本リポジトリを clone する．

```powershell
git clone https://github.com/Cream-Pan/kicad_my_library.git
```

以降，clone したフォルダを `kicad_my_library` とする．

### 2．フットプリントライブラリの登録

KiCad の PCB Editor を開き，以下の手順で登録する．

```text
Preferences
→ Manage Footprint Libraries
→ Add Existing
```

ライブラリパスには，clone したリポジトリ内の以下を指定する．

```text
./footprints/MyLibrary.pretty
```

Nickname は以下にする．

```text
MyLibrary
```

これにより，KiCad 上では以下の形式でフットプリントを参照できる．

```text
MyLibrary:C0603
MyLibrary:FPC-SMD_AFC07-S05FCC-00
MyLibrary:SOT-23-5_L2.9-W1.6-P0.95-LS2.8-BL
```

### 3．シンボルライブラリの登録

KiCad の Schematic Editor を開き，以下の手順で登録する．

```text
Preferences
→ Manage Symbol Libraries
→ Add Existing
```

ライブラリパスには，clone したリポジトリ内の以下を指定する．

```text
./symbols/MyLibrary.kicad_sym
```

Nickname は以下にする．

```text
MyLibrary
```

<p align="right">(<a href="#top">トップへ</a>)</p>

---

## GitHubへのアップロード方法

PowerShell で本リポジトリのディレクトリに移動する．

```powershell
cd path\to\kicad_my_library
```

Git リポジトリを初期化する．

```powershell
git init
```

`.gitignore` を作成する．

```powershell
@"
# KiCad temporary files
fp-info-cache
*.bak
*-bak
_autosave-*
*.lck

# OS files
Thumbs.db
.DS_Store
"@ | Out-File -Encoding utf8 .gitignore
```

管理対象のファイルを追加する．

```powershell
git add footprints
git add symbols
git add .gitignore
git add README.md
```

コミットする．

```powershell
git commit -m "Initial KiCad library"
```

GitHub 上に `kicad_my_library` リポジトリを作成した後，リモートリポジトリを登録する．

```powershell
git branch -M main
git remote add origin https://github.com/Cream-Pan/kicad_my_library.git
git push -u origin main
```

<p align="right">(<a href="#top">トップへ</a>)</p>

---

## 運用方法

### 1．フットプリントやシンボルを追加した場合

KiCad のフットプリントエディタやシンボルエディタで部品を追加・編集した後，PowerShell で変更内容を確認する．

```powershell
git status
```

変更を追加する．

```powershell
git add footprints
git add symbols
```

コミットする．

```powershell
git commit -m "Update KiCad library"
```

GitHub に反映する．

```powershell
git push
```

### 2．別PCで使用する場合

任意の場所にリポジトリを clone する．

```powershell
git clone https://github.com/Cream-Pan/kicad_my_library.git
```

その後，KiCad のライブラリ設定で以下を登録する．

```text
Footprint Library:
./footprints/MyLibrary.pretty

Symbol Library:
./symbols/MyLibrary.kicad_sym
```

### 3．KiCadプロジェクト側での注意

各 KiCad プロジェクトでは，シンボルやフットプリントを以下のように参照する．

```text
MyLibrary:部品名
```

例：

```text
MyLibrary:C0603
MyLibrary:MLX90632
MyLibrary:FPC-SMD_AFC07-S05FCC-00
```

ライブラリの Nickname を変更すると既存プロジェクトの参照が壊れる可能性があるため，`MyLibrary` の名称は変更しないことを推奨する．

<p align="right">(<a href="#top">トップへ</a>)</p>

---

## 設計上の工夫

### 1．ライブラリの一元管理

複数の KiCad プロジェクトで同じフットプリントやシンボルを使い回せるように，プロジェクトごとにライブラリをコピーせず，共通リポジトリとして管理する構成にしている．

### 2．Gitによる変更履歴管理

フットプリントやシンボルの変更履歴を Git で管理することで，過去の状態への復元や，発注時点のライブラリ状態の記録を可能にしている．

### 3．軽量な管理対象

本リポジトリでは，フットプリントとシンボルのみを管理対象とし，3Dモデルやプラグイン，個別プロジェクトは含めない．
これにより，リポジトリの肥大化を避けつつ，KiCad 設計に必要な自作ライブラリを管理できる．

<p align="right">(<a href="#top">トップへ</a>)</p>

---

開発者情報
Name: Takato Ishii

Portfolio: https://takato-ishii.vercel.app/
