# 公開デモ用 RAG Benchmark v1

このディレクトリは、架空の科学館資料だけを使ってBenchmark基盤の読み込み・実行・集計を確認するための公開可能なsmoke datasetです。

## 重要な位置づけ

`cases.jsonl`の9問はすべて`split: smoke`です。研究評価用の`test` splitではありません。既存の`evaluation_questions.json`をschema付き形式へ写し、gold sourceを付けた動作確認用データです。

この9問は既存システムのdistance threshold調整やデモに利用済みであるため、未知データに対する性能、汎化性能、研究上の有効性を示す結果として報告してはいけません。

## ファイル

- `case.schema.json`: 1問分のJSON Schema Draft 2020-12
- `cases.jsonl`: 3分類各3問、計9問のsmoke dataset
- `baseline_manifest.yaml`: 既存RAG、Model、設定、Corpusの再現用記録
- `requirements_snapshot.txt`: 初回実測時の直接依存関係を凍結した記録
- `initial_smoke_result.json`: 2026年8月11日の初回ローカル実測要約
- `README.md`: 利用範囲とラベル規則

## 対象Corpus

gold sourceには、`src.demo_data.DEMO_DOCUMENT_FILENAMES`で登録される次の架空資料だけを使用しています。

- `moon_phase.txt`
- `solar_system.txt`
- `black_hole.txt`
- `planetarium_guide.txt`
- `museum_faq.txt`

これらは`sample_docs/`に同梱されたデモ資料であり、実在する科学館の公式情報ではありません。

## 分類規則

| category | expected status | answerability | gold source |
|---|---|---|---|
| `answerable` | `answered` | `YES` | 1件以上 |
| `unrelated` | `low_relevance` | `NOT_RUN` | 空 |
| `insufficient` | `unanswerable` | `NO` | 話題上関連する資料を1件以上 |

`insufficient`のgold sourceは「その資料に答えがある」という意味ではありません。「正しい話題の資料を検索した上で、資料だけでは答えられないと停止する」ことを評価するための関連資料です。

## 読み込み

```python
from src.benchmark import load_benchmark_cases

cases = load_benchmark_cases("benchmarks/rag_v1/cases.jsonl")
```

Loaderはschema version、分類と期待値の整合、gold sourceの有無、空文字、重複IDを検査します。JSON Schemaファイル自体を検証する外部packageは必要ありません。

## 隔離されたSmoke run

Ollamaを起動し、Project rootで次を実行します。

```powershell
python -m src.benchmark.cli --run-id smoke-local-01
```

`outputs/benchmarks/smoke-local-01/`へ専用SQLite、Chroma、Interaction Log、Case結果、CSV、集計、Run Manifestを保存します。通常アプリの`data/`は使用しません。`outputs/`はGit管理外です。

初回実測は9問すべて期待Statusと一致しましたが、p95は68.3506秒でした。これは研究精度ではなく、Benchmark経路が実機で動くことと、Latency計測が次の課題であることを示すSmoke結果です。

## 研究用Datasetを作る場合

このファイルのsplit名だけを`test`へ変更して流用しないでください。別versionとして、未調整の質問、固定Corpus hash、`dev`とロックした`test`、ラベル作成手順を用意してください。thresholdやpromptの調整は`dev`だけで行います。
