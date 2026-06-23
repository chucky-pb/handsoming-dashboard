# ハンサミング 栄養士支援ダッシュボード（プロトタイプ）

栄養士・統括コーチ・本部の業務を支援する Web/モバイルのプロトタイプ。静的HTML（ビルド不要）＋ Supabase（DB・RPC・Edge Function）＋ Vercel（ホスティング）で構成。

## 公開URL
- 入口（役割と責務図ナビ）: `/index.html`
- モバイル版（LINE/統括/面談 の3役割）: `/mobile.html`
- 役割別UI（デスクトップ）: `/role_mockup.html`
- 資料・レポート棚: `/reports.html`
- 分析系: `/dashboard.html` `/interview.html` `/action_analysis.html` `/coach.html`

## 構成（ファイル＝画面）
| ファイル | 役割 |
|---|---|
| index.html | 役割と責務図の入口。staff.role で各ビューへ振り分け |
| mobile.html | モバイル専用。役割切替で LINEコーチ / 統括コーチ / 面談担当 |
| role_mockup.html | デスクトップの役割別UI（面談S-IV / LINE S-LC / 知見 / マニュアル検索 / 統括ホーム） |
| reports.html | レポート・設計資料のハブ |
| dashboard/interview/action_analysis/coach.html | 旧・分析系ダッシュボード |
| journey-map.html / roles-responsibilities-v1.pdf | 設計の地図 |
| *_report.html | リサーチ・分析レポート |

ビルド工程なし。HTMLを直接編集して反映。各HTMLは Supabase JS（CDN）を読み込み、anon キーでアクセス（RLSでガード）。

## バックエンド（Supabase）
- プロジェクト ref: `shmsyuohusfjjbopqokr`（リージョン: ap-northeast-1）
- 主なテーブル: `staff` `customers` `customer_processes` `interviews` `interview_analyses` `daily_reports` `early_warning_signals` `coach_comments` `handover_records` `knowledge_cards` `knowledge_reactions` `coach_notices` `manuals` `manual_chunks`
- 主なRPC: `line_checkin`（朝チェックイン4セグメント＋未返信判定） / `lc_karte`（個別カルテ要約＝引継ぎ・5回進捗） / `iv_list` `iv_karte`（面談一覧・カルテ） / `list_knowledge_cards` `search_knowledge_cards`（知見） / `search_manual_chunks`（pgroonga全文検索） / `get_coach_notices`（連絡）
- Edge Function: `manual-search`（マニュアルAI検索＝pgroonga取得＋Claude合成）
- 検索基盤: `pgroonga`（日本語全文検索）。`vector`(pgvector) も有効化可
- Edge Function のシークレット: `ANTHROPIC_API_KEY`（マニュアル検索のAI回答合成に使用。未設定でも全文検索の出典は返る）

## デプロイ
- 推奨: **Git連携による自動デプロイ**（main へ push で本番反映）
- 手動: `vercel --prod`（リポジトリルートで実行）
- Vercel プロジェクト: `handsoming-dashboard`（team: chucky-pb's projects）

## 秘密情報の扱い
- `.env.local`（Vercel OIDCトークン）と `.vercel/` は **コミットしない**（.gitignore 済み）
- HTML内の Supabase `anon` キーは公開可能キー（RLSでアクセス制御）。ただし RLS ポリシーは要見直し（プロト段階では緩め）

## プロトタイプの注意（プロト段階）
- デモデータが投入済み（`customers.source='line_demo'` 等）。本番運用前に整理が必要
- LINE連携は「取り込み（スマカリ→`daily_reports`へミラー）」のみがスコープ。送信はスマカリ側で完結
- 個人情報ライフサイクル（指導期間中フル／卒業後は抽象化）は設計済み・RLS実装は今後

## 設計ドキュメント（Notion）
- 役割別 機能設計 v0.9 / Intelligence Pipeline 設計書 / Phase 1 パイロット仕様 v2.1 / LINE連携 設計 v1.1 / あるべきLINE担当 体験設計 v1 / セッションログ
（リンクは reports.html を参照）

## 連絡先
PORTBACK 株式会社（開発: 樗木）
