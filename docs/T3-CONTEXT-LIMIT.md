# T3 Code: input token count exceeds

## What the error means

`400 context_length_exceeded` and `input token count exceeds ... 1048576` mean one request exceeded the context limit reported by the backend. This is different from account quota or requests per minute. More accounts or Parallel workers do not combine context windows.

In the case inspected on 25 October 2026, the original brief contained 13,497 characters and no embedded image data. The session later retained nine image tool outputs containing 1,878,849 data-URL characters. Characters are not token counts. The first error followed a batch of five image outputs. Twelve token-counter events lacked usage data and no compaction event was present. Changing models reused that conversation. The deployment owner confirmed its Studio node used Chat Completions.

## Why the route matters

T3's Codex driver uses Responses. The inspected Chatbox setup used Chat Completions. In pinned 9Router 0.5.104, Responses-to-Chat conversion stringifies non-string function output with `JSON.stringify(output)`. An array containing `input_image` and image data URLs can therefore become JSON/base64 text instead of retaining typed image parts.

A local reproduction used the actual 9Router package, fresh isolated data, random loopback ports, and a fixture backend. Identical Responses requests traversed two nodes:

| Node API type | Fixture received |
| --- | --- |
| Chat Completions | `/v1/chat/completions`, with the entire image base64 inside a stringified tool result |
| Responses | `/v1/responses`, with tool output retained as a typed `input_image` array |

Both fixture requests returned HTTP 200. This proves conversion at the tested router hop, not Google's token accounting. No Google requests, real credentials, or deployment edits were used. Local evidence was recorded in `.work/router-image-context-qa/evidence.json`; these local artifacts are not included in GitHub source.

The RDP's actual binary/payload was not inspected directly. A direct Responses node avoids the reproduced conversion, but successful multimodal inference on that deployment still needs its own verification.

## Create the additional Responses route

In Gemini Bridge Pro 1.7.4, keep the working Chat node and use the additional node:

1. On the backend host, select AI Studio and an accessible model. Keep the pool/router running and manual endpoints stopped.
2. Open **9Router → Provider settings → Responses route for Codex & T3**.
3. Choose `studio-responses` or a valid distinct prefix and select **Set up Responses route**.
4. Sign in through the app dialog if requested. The app verifies backend/catalog support, prepares its owned node/connection and model catalog, prepares a client key, and requests a reply through that prefix.
5. Inspect the completed nonempty reply and **Responses verified**, then copy the exact model ID.

Conflicts with another node/connection are rejected. This action does not create accounts, model entitlement, or a new backend. The node points to the AI Studio backend on the RDP; do not point it back to the public 9Router endpoint itself. Client T3 uses the public router endpoint and its client key in `NINEROUTER_API_KEY`, not the backend key.

The primary **Models & tests** result is distinct from the additional route's setup proof. Retest the extra route when changing models.

## Recover T3

Back up the actual `config.toml`. Change the top-level model and any active `gemini-bridge` profile to the verified `studio-responses/<model>` ID. Keep provider `nine-router`, the correct router endpoint, `wire_api = "responses"`, and `env_key = "NINEROUTER_API_KEY"`.

Register the same full ID in T3 provider Models, refresh, and start a **new conversation** with that model. Changing a route does not clear image data retained in an old conversation. See the [complete T3 setup](T3-CODE.md).

`404 No active credentials for provider: studio-responses` means the route lacks a usable active connection; inspect the server node, prefix, backend key, and connection state. Adding the model to T3 creates only a client choice.

A prefix does not save tokens by itself. Direct Responses preserves typed image outputs on the tested hop. Genuine oversized text history still needs reduction/compaction. Parallel controls the number of independent requests, not one conversation's context capacity.

## Test progressively

Start with a completed short reply, then one small image tool result that the model actually interprets. Add further image outputs gradually and inspect context/usage reporting. Test function calling and streaming separately before attempting the complete production brief.

For long jobs, save progress in project files, read only relevant sections, bound terminal output, and compact before the limit. Client settings such as `model_auto_compact_token_limit` and `tool_output_token_limit` do not enlarge server limits or repair malformed image conversion. Their behavior needs verification when model/usage metadata is absent.

References: [Gemini tokens/context](https://ai.google.dev/gemini-api/docs/tokens), [rate limits](https://ai.google.dev/gemini-api/docs/rate-limits), [Codex configuration](https://developers.openai.com/codex/config-reference/), [T3 context and compaction](https://github.com/pingdotgg/t3code/blob/main/docs/user/composer.md), [pool execution](EXECUTION.md).
