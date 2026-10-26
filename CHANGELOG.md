# Changelog

Berth is a restricted beta. Entries describe the distribution published here (chart, image, binaries).

## [0.5.1] - 2026-10-26

### Changed

- **Images and the Helm chart move to `ghcr.io/cloudguys-io`.** The GitHub organisation was renamed from `unishsys` to `cloudguys-io`, and the container registry does not redirect the old owner. Use `oci://ghcr.io/cloudguys-io/charts/berth` and `ghcr.io/cloudguys-io/berth`. The chart's default `image.repository` is now `ghcr.io/cloudguys-io/berth`; the 0.5.0 chart defaults to the old path, which no longer resolves, so an install of chart 0.5.0 needs `--set image.repository=ghcr.io/cloudguys-io/berth` or an upgrade to 0.5.1.
- Releases from 0.5.1 are signed by workflows in `github.com/cloudguys-io`. Update the `--certificate-identity-regexp` you pass to `cosign verify`.

### Fixed

- Log streams reconnect after the connection drops, with backoff and a fresh ticket, and skip the lines the server replays so nothing is shown twice. The view says when a stream is interrupted, and offers a Reconnect button when retries run out. Previously a dropped stream stopped updating with no indication.
- A change Kubernetes refuses is reported with its real status (403 permission denied, 404 not found, 409 conflict, 422 invalid) and an explanation, instead of a generic 500 and "Request failed with status code 500".
- Pressing Enter in the replica editor applied the new count to the table without sending it to the cluster. It now scales the deployment, and a refused change returns the row to the real count.

### Security

- Dependency updates for newly published advisories: `dompurify` (critical), `follow-redirects`, `markdown-it`, `vite` and the Quasar CLI in the dashboard; Go 1.27.3 and `golang.org/x/net` v0.60.0 in the server.

### Testing

- A browser-to-Kubernetes acceptance suite runs on every change: login and log-stream tickets, log reconnects, changes and their audit records, edit conflicts, refused writes (no token, no license, expired license, Kubernetes RBAC) and model provider failures.

## [0.5.0] - 2026-10-07

### Added

- Published evaluation data for the AI SRE in `evals/`: 17 scenarios, results, adjudication log, methodology and claims ledger.
- `cluster_health` includes a row per node (readiness and CPU/memory commit and use), capped at 12 nodes with a pointer to `analyze_capacity` for larger clusters.
- `describe_resource` accepts an optional `apiVersion` to read a custom resource; `list_resources` accepts no `kind` to discover the API kinds the cluster serves.
- Pricing rows and cache-read rates for Claude Opus 5.5, Sonnet 5.5 and Fable 5.1 in usage estimates.

### Changed

- **AI tool set reduced from 18 tools to 15.** `get_resource` is merged into `describe_resource`, `list_api_resources` into `list_resources`, and `list_nodes` into `cluster_health`. Tool definitions are about 26% smaller and the system prompt plus tools about 17% smaller.
- The default Anthropic model is `claude-sonnet-5-5` (was `claude-opus-5`) in the application and in the chart's `ai.anthropic.model`. A model saved in Settings, or pinned in your Helm values, is kept. The Bedrock default model and the `EFFORT` default are unchanged.
- Ollama: the forced final call after an exhausted tool loop now instructs the model to answer from the evidence already gathered.
- `AI_MAX_TOOL_ITERATIONS` now also bounds the Ollama tool loop, as its documentation always said. The default (8) is unchanged.
- Tool descriptions were shortened, and the text the assistant sees in tool output no longer names removed tools.

### Fixed

- Claude (Anthropic API and Amazon Bedrock): a run that reached the iteration limit while the model was still requesting tools could return an empty or preamble-only answer as a success. The last permitted call now withholds tools so the model answers; a reply cut off at the output limit is marked; an empty reply, or a run that ends still requesting tools, is reported as an error.
- Ollama: a 5xx from `/api/chat` (for example, a model emitting a malformed tool call) is retried once instead of failing the run.
- The cost estimate for Claude Opus 5.5 was 25% too high because it fell back to the older Opus price.
- The Claude engine now applies a default `max_tokens` of 8192 when none is configured.

### Removed

- The tools `get_resource`, `list_api_resources` and `list_nodes` (see Changed). The `tool` events streamed by the chat endpoint use the new names; integrations matching the old names need updating.

### Security

- No change to the security boundary. Raw logs and describes remain off unless `AI_ALLOW_RAW_DIAGNOSTICS` is enabled; Secrets and ConfigMaps are refused whatever arguments are passed (now covered by tests for the merged tools).

### Documentation

- Several statements about recommended models were reworded where our evaluation did not support them.

[0.5.1]: https://github.com/cloudguys-io/berth/releases/tag/v0.5.1
[0.5.0]: https://github.com/cloudguys-io/berth/releases/tag/v0.5.0
