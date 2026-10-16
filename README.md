# Seedream 4.0 API (`seedream-4-0`)

<!-- conv-kit:v1 -->

<p align="center">
  <img src="assets/badges/price.svg" alt="observed unit price"> <img src="assets/badges/billing.svg" alt="billing model"> <img src="assets/badges/compat.svg" alt="OpenAI-compatible endpoint">
</p>

<p align="center">
  <img src="assets/01-product-hero-perfume.jpg" width="820" alt="Sample render via the APIMart image API">
</p>

> **from $0.0195 per image** — `https://api.apimart.ai/v1` の OpenAI 互換エンドポイント1つだけ、月額プランは不要です。*(2026-10-12 時点)*

**[APIキー](https://go.apimart.ai/k-8899c8)** · **[料金](https://go.apimart.ai/k-e9a91c)** · **[モデルページ](https://go.apimart.ai/k-42bf7a)** · [⚡ 60秒クイックスタート](#quickstart)

**チームが APIMart 経由で Seedream 4.0（`seedream-4-0`）を使う理由**

- **キー1つでカタログ全体。**同じ `https://api.apimart.ai/v1` の base URL と `Authorization` ヘッダーで Seedream 4.0（`seedream-4-0`）と画像・動画・言語モデルなど 300+ のモデルにアクセスできます。切り替えるのは `model` フィールドだけで、クライアント側の変更は不要です。
- **最低 $1 から、従量課金。**サブスクリプションも、事前に規模を見積もるプリペイドプランもありません。$1 からチャージして、呼び出した分だけ使えます。
- **課金内容はレスポンスで返ります。**完了したタスクはすべて請求額（`cost` / `credits_cost`）を報告し、`failed` になったタスクは返金されます。
- **非同期前提の設計。**送信して `task_id` を受け取り、`GET /v1/tasks/{id}` をポーリングするだけ。バッチ処理もリトライも特別な統合ではなく、普通のキュー処理です。

<!-- /conv-kit:v1 -->

**別名:** シードリーム

Seedream 4.0 is ByteDance's unified text-to-image and image editing model — 4K output, industry-leading text rendering, batch processing and multi-reference support.

## Model id and routes

| Route | `model` value | Billing | Notes |
| --- | --- | --- | --- |
| Per-image | `seedream-4-0` | per delivered image, by resolution | see the [model page](https://go.apimart.ai/k-42bf7a) for the exact tiers |

Endpoint: `POST https://api.apimart.ai/v1/images/generations`, then poll `GET /v1/tasks/{task_id}`.

## Pricing

<!-- pricing:model:start -->
| Output | List price | Effective price |
| --- | --- | --- |
| default | 0.024375 | 0.0195 |


<!-- pricing:model:end -->

Prices are a snapshot; the [pricing page](https://go.apimart.ai/k-e9a91c) and [`data/model.json`](data/model.json) are refreshed by CI, and a completed task reports the exact amount in its `cost` field.

## Request parameters

| Field | Type | Default | Notes |
| --- | --- | --- | --- |
| `model` | string | required | the model id shown in the route table above |
| `prompt` | string | required | what to generate, or what to change when editing |
| `size` | string | `auto` / `1:1` | aspect ratio, or `auto` where the route supports it |
| `resolution` | string | `1K` | output resolution tier — cost scales with it |
| `n` | integer | `1` | number of images per task, where the route allows more than one |
| `image_urls` | string[] | — | reference images as public HTTP(S) URLs or base64 data URLs |
| `nsfw_check` | boolean | `false` | runs `omni-moderation-latest` before submitting |

The table lists the fields accepted by the APIMart `images/generations` endpoint. Check the [model page](https://go.apimart.ai/k-42bf7a) for anything this model adds on top.

## Quickstart

```bash
curl --request POST --url https://api.apimart.ai/v1/images/generations \
  --header "Authorization: Bearer $APIMART_API_KEY" --header 'Content-Type: application/json' \
  -d '{"model":"seedream-4-0","prompt":"A bamboo forest at sunrise, slow push-in","size":"1:1","resolution":"1K"}'
```

```python
import os, time, requests

BASE = "https://api.apimart.ai/v1"
HEADERS = {"Authorization": f"Bearer {os.environ['APIMART_API_KEY']}", "Content-Type": "application/json"}

created = requests.post(f"{BASE}/images/generations", headers=HEADERS, timeout=60, json={
    "model": "seedream-4-0", "prompt": "A bamboo forest at sunrise, slow push-in",
}).json()
task_id = created["data"]["id"]

while True:
    task = requests.get(f"{BASE}/tasks/{task_id}", headers=HEADERS, timeout=60).json()["data"]
    if task["status"] in ("completed", "failed"):
        break
    time.sleep(5)
print(task.get("cost"), task["result"]["images"][0]["url"])
```

Runnable versions: [`examples/`](examples). The task lifecycle is `pending → processing → completed | failed`.

## FAQ

**Seedream 4.0 のモデル id は何ですか？**

`seedream-4-0` です。上のルート表を参照してください。

**課金方法は？**

**from $0.0195 per image**。完了したタスクは `cost` フィールドに正確な金額を報告し、失敗したタスクは返金されます。

**How do I get a key?**

Use the [Get an API key](https://go.apimart.ai/k-8899c8) link — $1 minimum top-up, no plan required.

<!-- related-repos:auto -->
## Related repositories

- **Same model, this language** — the other half of this pair: [seedream-4-0-prompts-ja](https://github.com/g2xblz0ytan/seedream-4-0-prompts-ja)
- **Same model, other languages** — 9 more language builds: 中文 [seedream-4-0-api-cn](https://github.com/j6qn3i24az-9a/seedream-4-0-api-cn) · Deutsch [seedream-4-0-api-de](https://github.com/b2n87l0aoum6e/seedream-4-0-api-de) · Español `seedream-4-0-api-es` · Français [seedream-4-0-api-fr](https://github.com/johv6uyc/seedream-4-0-api-fr) · Relay/gateway `seedream-4-0-api-gateway` · Bahasa [seedream-4-0-api-id](https://github.com/ccyzyl/seedream-4-0-api-id) · 한국어 [seedream-4-0-api-ko](https://github.com/tl6kdw/seedream-4-0-api-ko) · Português `seedream-4-0-api-pt` · Русский `seedream-4-0-api-ru`
- **More models in this language** — [midjourney-api-ja](https://github.com/apimartt1q/midjourney-api-ja) · [gemini-2-5-flash-image-preview-api-ja](https://github.com/ic7skkh/gemini-2-5-flash-image-preview-api-ja) · [nano-banana-api-ja](https://github.com/e4ylndl3g-v/nano-banana-api-ja) · [gemini-3-1-flash-image-preview-api-ja](https://github.com/gv4ju/gemini-3-1-flash-image-preview-api-ja) · [nano-banana-2-api-ja](https://github.com/lpbxkrzbfel/nano-banana-2-api-ja) · [gemini-3-pro-image-preview-api-ja](https://github.com/zzsxs8r/gemini-3-pro-image-preview-api-ja)
- **Topic hubs** — every build of this model, all languages: [seedream-4-0](https://github.com/topics/seedream-4-0) · [seedream-4-0-api](https://github.com/topics/seedream-4-0-api)

This repository is one build in a cross-linked set of API guides for the same model family; each language build links back to the others.
<!-- /related-repos:auto -->

## Attributed links (how this repository is measured)

| Purpose | Attributed link | Target |
| --- | --- | --- |
| Model page | <https://go.apimart.ai/k-42bf7a> | `apimart.ai/ja/model/seedream-4` |
| Live pricing | <https://go.apimart.ai/k-e9a91c> | `apimart.ai/ja/pricing` |
| Get an API key | <https://go.apimart.ai/k-8899c8> | `apimart.ai/ja/keys` |

Outbound APIMart links are minted through the promo link API; hand-made tracking parameters are rejected by
`tools/check_links.py` in CI.

## Disclosure

Seedream 4.0 is a third-party model served through APIMart; this repository documents how to call it and publishes
model ids and prices, and does not claim official status. Model names, prices and documentation belong to their
respective owners.

## License

MIT — see [LICENSE](LICENSE).
