# Third-party notices

このRepositoryは、次の主要な第三者packageを利用します。
表は2026-08-20時点で、各packageのmetadataと公式情報を確認した記録です。
各packageには、それぞれのlicenseが適用されます。

| Package | Version | License | 用途 |
|---|---:|---|---|
| Streamlit | 1.60.0 | Apache-2.0 | Web UI |
| ChromaDB | 1.5.9 | Apache-2.0 | local vector store |
| PyMuPDF | 1.28.0 | GNU AGPL-3.0 or Artifex commercial license | PDF text extraction |
| HTTPX | 0.28.1 | BSD-3-Clause | local Ollama HTTP client |
| PyYAML | 6.0.3 | MIT | configuration loading |
| pytest | 9.0.3 | MIT | development tests |
| Ruff | 0.16.3 | MIT | linting |

PyMuPDFの公式license説明では、GNU AGPLまたはArtifexのcommercial licenseが
適用されます。この依存関係との整合を明確にするため、本Repository自体も
GNU Affero General Public License v3.0で公開します。商用製品へ組み込む場合は、
PyMuPDFを含む各依存関係の条件を利用者自身で確認してください。

完全なtransitive dependency一覧は、対象環境で次を実行して確認できます。

```powershell
python -m pip install pip-licenses
pip-licenses --format=markdown --with-urls
```

この文書はlicense情報の整理であり、法的助言ではありません。
