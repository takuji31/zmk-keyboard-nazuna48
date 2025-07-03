# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## プロジェクト概要

Nazuna48キーボード用のZMK（Zephyr Mechanical Keyboard）ファームウェアリポジトリです。48キーの分割ワイヤレスキーボードで、ドングル対応機能を備えています。Auto-Keyboard-Design-Kitで生成され、直接ワイヤレス接続とスクリーン表示付きワイヤレスドングル操作の両方に対応しています。

## ビルドコマンド

- **全ファームウェアビルド**: 変更をプッシュするとGitHub Actionsが自動的にファームウェアをビルドします
- **手動ビルド**: `build.yaml`設定を使用してZMKの標準ビルドプロセスを使用
- **ビルドターゲット**:
  - `akdk_bt1` + `nazuna48_left` - 左半分のファームウェア
  - `akdk_bt1` + `nazuna48_right` - 右半分のファームウェア
  - `seeeduino_xiao_ble` + `nazuna48_dongle dongle_screen` - スクリーン付きワイヤレスドングル
  - 両ボードタイプ用の設定リセットファームウェア

## アーキテクチャ

### ハードウェア構成
- **分割キーボード**: 合計48キー（片側24キー）
- **コントローラー**: キーボード半分用に2つのワイヤレスコントローラー（akdk_bt1）
- **ドングル**: スクリーン表示対応のSeeeduino XIAO BLE
- **マトリックス**: 合計4行×12列
- **接続**: 半分とドングル間はBluetooth Low Energy

### ファームウェア構造

**ボード定義** (`boards/arm/akdk_bt1/`):
- キーボード半分で使用されるワイヤレスコントローラー用のカスタムボード定義

**シールド定義** (`boards/shields/nazuna48/`):
- `nazuna48_left.*` - GPIOマトリックスマッピング付き左半分設定
- `nazuna48_right.*` - 列オフセット付き右半分設定
- `nazuna48_dongle.*` - モックkscanとスクリーン対応の中央コントローラー
- `nazuna48.zmk.yml` - siblingsと機能を定義するシールドメタデータ
- `layouts.dtsi` - キー位置を含む物理レイアウト定義
- `Kconfig.*` - 分割機能用のビルド設定

**主要設定ポイント**:
- ドングル付き分割キーボードは2つのペリフェラルを管理する中央デバイスとして動作
- ドングルは表示機能に`zmk-dongle-screen`モジュールを使用
- 全バリアントがZMK Studio設定に対応
- ドングル用にバッテリーレポートとBLE中央管理が設定済み

### 依存関係
- **ZMK**: メインファームウェアフレームワーク
- **zmk-dongle-screen**: ドングルスクリーン機能用外部モジュール（janpfischer/zmk-dongle-screen）

### 設定ファイル
- `build.yaml` - 全ビルドターゲットとその設定を定義
- `config/west.yml` - ZMKと外部依存関係を含むWestマニフェスト
- `config/keymap.keymap` - メインキーマップ設定
- 各シールドバリアント用の個別`.conf`ファイル（機能設定用）