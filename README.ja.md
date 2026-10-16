# FreeChemDraw MCP Server

[English](README.md) | [简体中文](README.zh-CN.md) | [日本語](README.ja.md)

**AI エージェントのための化学構造ツール**

FreeChemDraw MCP Server の公式ドキュメントと接続例です。リモートでの化学構造変換、分子物性計算、二次元構造描画を紹介します。

このリポジトリでは FreeChemDraw のホスト型 MCP サービスのドキュメントと連携例を提供します。サーバー実装は非公開で管理されています。

[FreeChemDraw](https://freechemdraw.com/) |
[製品紹介](https://freechemdraw.com/en/mcp/) |
[公式ホスト型プラットフォーム](https://ai.freechemdraw.com/) |
[公式ドキュメント](https://ai.freechemdraw.com/docs/)

Remote MCP エンドポイント：`https://ai.freechemdraw.com/mcp`

## クイックスタート

1. リモート Streamable HTTP に対応するクライアントを選びます。
2. サーバー URL を次の値に設定します。

   ```text
   https://ai.freechemdraw.com/mcp
   ```

3. 接続してツールを検出し、クライアント経由で次のエタノールの例を呼び出します。

   ```json
   {"name":"calculate_properties","arguments":{"structure":"CCO","input_format":"smiles"}}
   ```

このオブジェクトは `tools/call` の引数であり、完全な HTTP/JSON-RPC リクエストではありません。
初期化とプロトコル交渉はクライアントが行います。物性値は計算結果であり、実験測定値ではありません。
[入門ガイド](docs/getting-started.md)を参照してください。

## FreeChemDraw MCP とは？

FreeChemDraw MCP Server は、AI エージェントとアプリケーション向けのホスト型サービスです。
提供者は FreeChemDraw、分類はリモート MCP サーバー / 化学 REST API です。
Model Context Protocol（MCP）と Streamable HTTP を使用し、対応クライアントから化学ツールを検出・呼び出せます。
このリポジトリには利用ガイドとリクエスト例のみを収録し、サーバー実装や化学計算エンジンは提供しません。

## 機能

- 現在の公開契約では、基本的な化学処理を無料・匿名で利用できます。レート制限が適用され、登録や API Key は不要です。
- SMILES の正規化、SMILES/MOL 構造の変換。
- 分子式、分子量、精密質量、形式電荷、LogP、TPSA の計算。
- SVG/PNG 形式の二次元分子構造図の生成。
- Remote MCP または REST による AI エージェントのワークフローへの組み込み。

アクセス方針と実際の制限はホスト側の設定に従います。[機能情報](https://ai.freechemdraw.com/api/v1/capabilities)と現在の[サービス文書](https://ai.freechemdraw.com/docs/)を確認してください。

## 利用可能なツール

| 基本ツール | 機能 |
| --- | --- |
| `resolve_chemical` | SMILES の正規化、分子式、任意の物性情報 |
| `convert_structure` | SMILES/MOL 構造の変換 |
| `calculate_properties` | 分子式、分子量、精密質量、形式電荷、LogP、TPSA |
| `render_structure` | デフォルト様式による SVG/PNG 二次元構造描画 |

`get_editor_link` は任意のツールで、ホスト側で有効な場合のみ表示されます。
暗号化された構造の一時保存と一度限りの取り込みには、利用者の明示的な同意
（`allow_temporary_storage=true`）が必要です。リンクの有効期限は 10 分です。
利用前に `tools/list` を確認してください。代わりにオンラインエディターへ SMILES/MOL を手動で取り込むこともできます。
[ツール](docs/tools.md)と[プライバシー](docs/privacy.md)を参照してください。

## FreeChemDraw で編集を続ける

1. エージェントに正規化済みの SMILES を返すよう依頼するか、`convert_structure` の `output_format=mol` で MOL 構造を取得します。
2. [FreeChemDraw オンラインエディター](https://freechemdraw.com/)を開き、SMILES を貼り付けるか、保存した MOL ファイルを取り込みます。
3. エディターで構造を確認し、編集を続けます。MOL から SMILES への変換では座標や注釈が失われるため、それらを保持したい場合は MOL を使用してください。

自動編集リンクは、実際のホスト環境の `tools/list` に `get_editor_link` が表示される場合にのみ利用できます。
構造の暗号化された一時保存には利用者の明示的な同意が必要で、リンクは一度限り有効、10 分で失効します。
ツールが無効な場合や同意がない場合は手動で取り込んでください。クライアントとエディターの自動同期は保証しません。
[ツール](docs/tools.md)と[プライバシー](docs/privacy.md)を参照してください。

## MCP クライアント設定

[設定例](examples/mcp-client-config.json)はベンダーに依存しない接続記述です。
`transport=streamable-http` と上記 URL を、クライアントの現在のリモートサーバー設定に入力してください。
JSON の項目名はクライアントごとに異なります。Claude、Cursor などの製品で認証済みの、そのまま読み込める設定ではありません。
stdio コマンドや旧式の SSE 専用トランスポートは使用しません。[Remote MCP](docs/remote-mcp.md)を参照してください。

## REST API

ホスト型の化学 REST API に JSON を送信します。REST では MCP のリクエスト形式を使用しません。

```bash
curl --fail-with-body https://ai.freechemdraw.com/api/v1/properties/calculate -H 'Content-Type: application/json' --data '{"structure":"CCO","input_format":"smiles"}'
```

成功時のレスポンスには `request_id`、`data`、`error=null`、`usage.calls=1` が含まれます。
`usage.calls` は呼び出し回数であり、有料クレジットではありません。
[REST API](docs/rest-api.md)とホスト型の[OpenAPI 契約](https://ai.freechemdraw.com/openapi/v1.json)を参照してください。

## 利用制限

無料の基本サービスにはレート制限があり、サービスレベルは保証されません。
初期化とツール検出も利用量に含まれます。HTTP 429 では `Retry-After` に従い、間隔を延ばして再試行してください。
詳細な参照値とエラー処理は[利用制限](docs/usage-limits.md)、本番環境の実効値は現在の[公式サービス文書](https://ai.freechemdraw.com/docs/)で確認してください。

## プライバシー

リクエストは構造をホスト型サービスへ送信します。通常の計算では元の構造や画像を永続保存しませんが、
呼び出しメタデータはサービス方針に従って保持されます（参照保持期間：30 日）。
クライアントやプロキシは独自にデータを保持する場合があります。任意のエディターリンクでは、明示的な同意後にのみ構造を暗号化して一時保存します。
リモート処理は絶対的な機密保持を保証しません。[プライバシー説明](docs/privacy.md)と[公式方針](https://ai.freechemdraw.com/privacy)を参照してください。

## ドキュメント

- [入門ガイド](docs/getting-started.md)
- [ツール、引数、結果フィールド](docs/tools.md)
- [Remote MCP とクライアント設定](docs/remote-mcp.md)
- [REST API](docs/rest-api.md)
- [利用制限とエラー](docs/usage-limits.md)
- [プライバシー](docs/privacy.md)
- [トラブルシューティング](docs/troubleshooting.md)
- [よくある質問](docs/faq.md)
- [変更履歴](CHANGELOG.md)
- [貢献ガイド](CONTRIBUTING.md)
- [セキュリティ報告](SECURITY.md)

## 制約事項

名称/CAS 検索は利用できません。検証済みの描画様式はデフォルトのみで、ACS 専用様式には対応しません。
任意のエディター連携は、常に利用できる第五の基本ツールではありません。
クライアント製品の公式認証、無制限の利用、すべての分子の科学的検証は主張しません。
このリポジトリはサーバー実装を配布せず、その利用許諾も付与しません。

## Issues と貢献

[Issues](https://github.com/chemaiatlas/freechemdraw-mcp/issues)では文書の修正、公開構造による最小再現例、機能提案を受け付けます。
貢献対象はドキュメントと安全に共有できる接続例です。[貢献ガイド](CONTRIBUTING.md)を参照してください。
脆弱性は [SECURITY.md](SECURITY.md)に従い非公開で報告してください。
製品ブランドは FreeChemDraw です。リポジトリは ChemAI Atlas Organization に置かれています。
