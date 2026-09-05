# TOEIC Project

TOEIC L&Rの問題学習、回答、正誤・解説確認、FSRSを利用した復習、成績と学習状況の可視化を行うローカル学習アプリです。学習者向けのWeb UIを中心に、教材生成・管理の経路は学習画面から分離しています。

このプロジェクトは個人開発であり、業界実務経験として制作したものではありません。コード・文章化・調査・技術評価には生成AIを利用したAI支援開発を採用しています。開発開始：2026年7月2日（記録あり）／現在も継続中。

## 主な機能

- TOEIC Part 2〜7の問題学習、回答、正誤・解説の確認
- Part 2〜7の明示的なPart選択とPart別学習セッション
- Part 2 / 3 / 4のListening音声再生
- Part 6 / 7の本文・文書単位を保持したReading学習
- FSRSベースの復習と復習期限の管理
- 復習期限、弱点、未学習問題をもとにした「今日の学習」導線
- Part別正答率、日別活動、学習セッション、今後の復習予定の可視化
- ローカルで動作する学習者向けWeb UI
- 教材生成の実行（run）の作成・停止・レビュー後の明示的な再開
- 失敗した生成単位（unit）をレビュー待ち（`NEEDS_REVIEW`）として扱う状態管理

## 現在の学習対象

現在の `ACTIVE_STUDY` はPart 2〜7の合計194問です。

| Part | 問題数 |
| --- | ---: |
| Part 2 | 25 |
| Part 3 | 39 |
| Part 4 | 30 |
| Part 5 | 30 |
| Part 6 | 16 |
| Part 7 | 54 |
| **合計** | **194** |

Part 1は現在のポートフォリオ対象外です。

## 技術スタック

| 技術 | 用途 |
| --- | --- |
| Python | 学習ロジック、状態管理、CLI |
| HTML / JavaScript | 学習者向けWeb UI |
| PowerShell | Windows上の起動・運用 |
| Git | 差分・履歴管理 |
| pytest / Ruff等 | 検証 |
| 生成AIツール（LLMツール） | 実装・レビュー・技術調査 |

## 人間 / AIの役割分担

| 項目 | 主体 |
| --- | --- |
| 目的設定・範囲・損切り | 人間（Human） |
| 不可逆な操作の承認 | 人間（Human） |
| 予算・公開方針 | 人間（Human） |
| 技術設計・調査・実装手順の作成 | AI |
| 実装 | AI |
| 検証・実測 | ツール / AI |
| 最終的な公開・製品判断 | 人間（Human） |

コード・文章化・調査・技術評価には生成AIを利用しています。人間（Human）は、目的、範囲、損切り、不可逆な操作、予算、公開方針など、自身が判断可能かつ所有すべき境界を担当します。技術的な設計、実装、検証には複数のAIモデルと自動検証を用途に応じて使い分けています。

## 技術的な特徴

### AIによる実装と検証を分離

実装を担うAIの自己申告だけで完了とせず、テスト、実行結果、差分、実ブラウザでの確認など、変更内容に応じた検証経路を分離しています。

### 実行範囲の制限と正本への反映を分離

検証用・制限付きワークスペースと正式な開発リポジトリ（正本リポジトリ）を分け、AIが変更できる範囲と、正式な開発リポジトリへ反映する境界を分離しています。

### 条件不成立時に停止し、再判断を求める

外部AIサービスへの接続、出力データ形式、品質検証などが失敗した場合は自動継続せず、`NEEDS_REVIEW` や `STALLED` として停止して確認を要求します。

### 部分成功を全体成功へ昇格させない

個別の検証軸が成立しても、出力契約などの全体条件が成立しなければ、統合全体の成功とは扱いません。

## 代表ケーススタディ

1. **[ローカルAI実装環境の選定](docs/portfolio/LOCAL_CODING_AGENT_ADOPTION_REPORT.md)**
   速度だけでなく、書き込み範囲、停止指示の遵守、正式な開発リポジトリへの反映境界まで比較しました。
2. **[Claude reviewer統合](docs/portfolio/CLAUDE_CODE_REVIEWER_INTEGRATION_REPORT.md)**
   認証成功と出力成立を別々に検証し、個別のPASSを統合全体のPASSへ昇格させない構成を確認しました。
3. **[AIエージェント事故分析](docs/AI_INCIDENT_REPORT.md)**
   自然言語上の禁止と、実行環境側で強制される制限が別の境界であることを事故記録から検証しました。

## 詳細資料への入口

現時点で存在する既存資料への入口です。READMEの要約は紹介用の表示層であり、詳細な技術・履歴の正本を置き換えません。

- [学習者向けUIの情報設計と実装](docs/ui.md)
- [アーキテクチャ](docs/architecture.md)
- [運用手順](docs/operations.md)
- [ポートフォリオ用デモガイド](docs/portfolio/DEMO_GUIDE.md)

## 現在の状態

Part 2〜7の194問を `ACTIVE_STUDY` に採用し、学習者向けWeb UIから6 Partすべてを開始できる状態です。

実行・適用後の検証では、ライブ検証、194問・6セッション・194 ratingsのスモークテスト、関連テストがPASSしています。

Part 2 / 3 / 4ではListening音声を利用し、Part 6 / 7では本文・文書単位を保持して学習できます。問題回答、正誤確認、解説、FSRS評価、保存、復習、成績確認まで通常の学習経路へ接続されています。

教材生成は学習者向けUIとは分離した管理用経路で扱い、失敗した生成単位（unit）は自動継続せず、レビュー待ちとして停止します。

Part 1の学習機能は未対応です。

## 検証済み範囲と未実施範囲

現在の194問ポートフォリオについて、runtimeへの接続、Part別session、回答、採点、評価、保存、主要UI経路は検証済みです。

一方、以下は今回の完成条件から明示的に除外しています。

- Part 2音声25本の人手による全件聴取確認
- 194問全件に対する独立したsemantic review

したがって、本プロジェクトはAI生成教材を利用する非公式の学習アプリであり、全教材が専門家検証済み、公式TOEIC同等、または意味品質を全件独立検証済みであるとは主張しません。

## 既知の限界

以下は現時点で明示されている限界です。

| 限界 | 意味 |
| --- | --- |
| Part 1未実装 | TOEIC Part 1の学習機能は未対応 |
| semantic review未実施 | 現在の194問全件について独立した意味品質レビューは実施していない |
| Part 2音声の全件人手聴取未実施 | 25本すべてを人手で聴取して意味対応を確認する工程は実施していない |
| psychometric未較正 | 問題難易度等の統計的較正は未完了 |

## 実行方法

ダブルクリック：

```text
launch webUI.bat
```

これは現行の学習者向けWeb UI（HTMLアプリケーション）をローカルで起動し、既定のブラウザで開きます。

```powershell
Set-Location E:\toeic_project
.\tools\toeic.ps1 ui
.\tools\toeic.ps1 ui --port 8765      # ポートを固定する場合
.\tools\toeic.ps1 ui --no-browser     # ブラウザを自動で開かない場合
```

UIの正本designは `sol ultra\toeic-study-shell.html` です。画面の実体は
`src/toeic_project/webui/` にあり、`127.0.0.1` にのみbindします。外部へは公開されません。

CLI:

```powershell
.\tools\toeic.ps1 preflight
.\tools\toeic.ps1 new
.\tools\toeic.ps1 status <run_id>
.\tools\toeic.ps1 stop <run_id>
.\tools\toeic.ps1 resume <run_id>
```

標準起動画面は学習者向けの「ホーム」です。今日の復習、弱点、未学習問題から
決定論的に次の学習を提案します。生成runは「設定・管理」に分離されています。

`new` と `resume` は管理機能であり、generation worker とread-only monitorを
detached起動します。
失敗した unit は自動再試行せず `NEEDS_REVIEW` になり、人間が確認後に明示的に
`resume` します。

## 開発・運用の基準

実装時の必須境界は [AGENTS.md](AGENTS.md)、現在状態の記録は
[引継ぎ/current/toeic_project_current_state.txt](引継ぎ/current/toeic_project_current_state.txt)、開発開始日の記録は
[引継ぎ記録](引継ぎ/current/TOEIC_PROJECT_CHAT_HANDOFF.md)、AI運用事故の記録は
[docs/AI_INCIDENT_REPORT.md](docs/AI_INCIDENT_REPORT.md) を参照してください。
