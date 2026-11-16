# EmDash MCP Reference (`tech.anti-pattern.co.jp`)

## Endpoint & Token Scopes

* Endpoint: `https://tech.anti-pattern.co.jp/_emdash/api/mcp` (stateless Streamable HTTP JSON-RPC 2.0).
* Recommended least-privilege PAT (`ec_pat_...`) scopes for drafting with images:
  * `content:read`, `content:write`
  * `schema:read`
  * `taxonomies:manage`
  * `media:read`, `media:write`
  * (`mcp:tools` is only for third-party plugin tools, not core EmDash tools.)
* Register in Antigravity CLI without putting tokens in git/articles:
  ```bash
  agy mcp add --header "Authorization: Bearer <TOKEN>" emdash https://tech.anti-pattern.co.jp/_emdash/api/mcp
  ```
* If calling the endpoint from a shell script, use `curl` rather than Python `urllib` (Cloudflare WAF may reject Python's default User-Agent with `403`). Never echo the token to stdout.

## `posts` Collection Conventions

* **Primary Byline**: `Kohei Kondo` (`slug: kooooohe`, look up current ID via `byline_list`).
* **Fields**:
  * `data.title` (string, required)
  * `data.content` (Markdown string — EmDash converts headings, tables, blockquotes, code blocks, and `/_emdash/api/media/file/...` images to Portable Text)
  * `data.featured_image` (media ID string returned by `media_upload`)
  * `data.excerpt` & `data.custom_excerpt` (short summary text)
  * `data.tag_order` (comma-separated tag slugs matching `taxonomies.tag`, e.g. `"emdash,cloudflare,astro,mcp,gatsby"`)
  * `data.reading_time` (integer minutes)
* **SEO**: `seo.title`, `seo.description`, `seo.image` (`/_emdash/api/media/file/...` URL).
* **Updates**: `content_update` requires `_rev` obtained from `content_get`.
