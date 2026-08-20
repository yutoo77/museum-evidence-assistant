# Security policy

## 対象となる利用形態

Museum Evidence Assistantは、信頼できる利用者が単一PCで使用する研究プロトタイプです。
Streamlitは`127.0.0.1`へだけbindし、設定で許可するOllama接続先もloopbackへ
限定しています。認証、利用者ごとの権限管理、複数人同時利用は実装していません。

次の運用は想定外です。

- Internetや館内LANへ直接公開する
- 不特定多数が文書をuploadできる状態にする
- 機密情報を含むPCで、信頼できない文書を登録する
- 本アプリの回答を職員確認なしで公式回答として利用する

## データの保存

実行時データはGit管理外の`data/`へ保存されます。

- `data/documents/`: 登録した原本
- `data/chroma/`: 検索用本文とembedding
- `data/app.db`: 資料台帳
- `data/logs/interactions.jsonl`: 質問、回答案、判定、根拠識別情報、feedback
- `data/logs/app.log`: 障害情報

Interaction logは根拠本文全体を保存しませんが、**質問と回答案は保存します**。
個人情報や機密情報を質問へ含めないでください。Repositoryを共有する前に、
Gitの追跡対象だけでなく`data/`と`outputs/`の内容も確認してください。

## 実装上の防御

- Ollama URLは`localhost`またはloopback IPだけを許可
- Streamlitは`127.0.0.1`へbind
- Chroma telemetryとStreamlit usage statisticsを無効化
- upload filenameからdirectory成分を除去し、保存名にはUUIDを使用
- upload sizeと対応拡張子を検証
- `yaml.safe_load`を使用
- 生成前にdistance gateと回答可能性gateを通し、根拠不足時は回答を停止
- 思考過程を画面やlogへ保存しない

## 既知の依存関係リスク

2026-08-20時点で、`chromadb==1.5.9`には
[CVE-2026-45829](https://github.com/advisories/GHSA-f4j7-r4q5-qw2c)が報告され、
修正版は公開されていません。この問題はChroma HTTP Serverのcollection作成APIで
`trust_remote_code`を受け付ける場合のcode injectionです。

本プロジェクトはChroma Serverを起動せず、Pythonプロセス内の
`PersistentClient`だけを使用します。そのため、報告されているHTTP endpointを
公開しません。ただし、upstreamの修正版が公開されたら更新し、再監査します。

## 残るリスク

- 登録文書に含まれるprompt injectionへの完全な防御はない
- 小型LLMは根拠にない内容を生成したり、回答可能性を誤判定したりする場合がある
- PDF parserは複雑なPDFや不正なPDFを完全には安全に扱えない
- distance thresholdは資料集合ごとの校正が必要
- local bindは、同じPC上の別processからのaccessを防ぐものではない

## 脆弱性の報告

脆弱性を見つけた場合は、公開Issueへ機密情報や再現用の実データを貼らず、
[GitHub Private Vulnerability Reporting](https://github.com/yutoo77/museum-evidence-assistant/security/advisories/new)
から報告してください。

この文書は技術上の脅威モデルを示すもので、法的助言ではありません。
