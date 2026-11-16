---
name: kohe-emdash-blog
license: MIT
description: >-
  Writes, sanitizes, illustrates, and saves engineering blog drafts to EmDash
  (tech.anti-pattern.co.jp) in kohe's personal writing style. Use when the user
  asks to turn notes, HTML summaries, or work logs into a tech blog post in
  kohe's tone, generate thumbnails/architecture diagrams, or publish/draft posts
  via EmDash MCP.
---

# kohe's EmDash Blog Writing Workflow

Write grounded engineering blog posts for `tech.anti-pattern.co.jp` in `kohe` (`Kohei Kondo` / `kooooohe`)'s voice, generate crisp visuals, sanitize internal information, and save drafts via EmDash MCP.

## 1. Voice, Structure & Anti-AI Rules

* **Opening & Identity**: Start with `こんにちは、koheです。`. Do **not** include links to Zenn (`zenn.dev/...`) unless explicitly requested.
* **Tone**: `です・ます` base with candid engineer remarks and `※ 〜` side notes. Keep paragraphs short (2–4 lines) and use `##` / `###` headings plus Markdown tables for metrics or comparisons.
* **No AI Clichés or Unnatural Words**:
  * Avoid stiff/bureaucratic terms such as `正本` (use `元データ`, `ビルド済み出力`, etc.).
  * Strip typical AI-blog phrases: `結果から言うと`, `理由はシンプルで`, `今どきっぽい`, `〜と言った点`, `完遂できました`, `非常に軽量で`, `〜の心配もほぼありません`, `いかがでしょうか`, generic closing sign-offs (`引き続き〜をよろしくお願いします！`).
* **Quotes & Citations**: Quote primary official docs verbatim in `>` blockquotes with `— [Title](URL)`. Verify exact wording from the source page before quoting; never paraphrase inside `>`.
* **Explain Trade-offs & Irregular Uses**: When using beta tooling or repurposing a service outside its textbook use case (e.g., `cf` CLI beta, or Cloudflare for SaaS used for single-subdomain routing instead of multi-tenant SaaS), explicitly contrast the standard use case with why it was chosen here.

## 2. Sanitization Checklist (Public Safety)

Before saving or uploading, strip or generalize:
1. Unrelated internal zones, hostnames, and sibling service domains (replace with `example.net` / `<別ゾーン>`).
2. Private repo names, internal issue/PR numbers, commit hashes, internal Worker/D1/R2 names, and internal tracking IDs.
3. API tokens, credential rotation/leak notes, internal Slack DM details (headcounts, deadlines, wording mismatches), incomplete deploy timestamps, and internal WAF/Access posture notes.

## 3. Thumbnails & Diagrams (No ASCII Code Blocks)

* **Never use ASCII art inside fenced code blocks** for architecture/flow diagrams—EmDash renders a `Copy` button on code blocks.
* **Architecture / Flow Diagrams**:
  1. Build clean HTML + SVG diagrams (using `overflow: hidden` on `html, body` and a `--window-size` larger than the card so Chrome never draws scrollbars).
  2. Render high-DPI PNGs via `google-chrome --headless=new --disable-gpu --no-sandbox --force-device-scale-factor=2 --screenshot=<out.png> file://<in.html>` followed by `convert <out.png> -trim +repage <out.png>`.
  3. Visually inspect every PNG with `read_file` to verify zero scrollbars, clipped labels, or overlaps.
* **Cover Thumbnail (`featured_image`)**:
  1. Invoke `image-generator` for a 16:9 editorial tech illustration with **no text** (to avoid garbled characters).
  2. Inspect the generated image with `read_file` and copy it to the workspace `images/` directory.

## 4. EmDash MCP Drafting & Verification

See [references/emdash-mcp.md](./references/emdash-mcp.md) for scopes, schema fields, and API quirks.

1. **Always save as `status: "draft"`** unless explicitly told to publish.
2. **Lookup Author & Tags**: Resolve `Kohei Kondo` (`slug: kooooohe`) via `byline_list`, check existing tags with `taxonomy_list_terms`, and create missing tags via `taxonomy_create_term`.
3. **Upload Media**: Upload thumbnail and diagrams via `media_upload`.
4. **Create / Update Post**:
   * Pass Markdown with `/_emdash/api/media/file/...` image URLs for `data.content` (EmDash converts them to Portable Text `image` blocks with `asset._ref`).
   * Set `data.featured_image` (thumbnail media ID), `seo.image` (thumbnail URL), `data.excerpt`, `data.custom_excerpt`, `data.tag_order` (comma-separated tag slugs), `bylines`, `taxonomies`, and `seo`.
   * When updating an existing draft, call `content_get` first and pass `_rev` to `content_update`.
5. **Verify**: Call `content_get` after writing to confirm `featured_image`, `seo.image`, `bylines`, and every `image` block's `asset._ref` are populated.
