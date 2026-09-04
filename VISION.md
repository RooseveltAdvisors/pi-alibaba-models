# Vision

This is the captain's fork of Fornace/pi-alibaba-models, the complete Alibaba provider for pi, and it exists because the fleet runs its Alibaba work through pi every day and cannot wait on a release cycle to keep that working.
Upstream already builds the provider well: Model Studio Coding Plan and pay-per-token DashScope side by side, Anthropic- and OpenAI-shaped APIs, International and China endpoints, live model catalogs, native thinking levels.
The fleet's pain was specific: Alibaba's OpenAI-compatible endpoints reject the `developer` role, so the instructions agents send as developer messages died at the API with a role error, and every pi launch paid two cross-region round trips to Singapore before the first prompt.
Both failures are invisible to a human typing in a terminal and fatal to an unattended agent, which is exactly why the fork exists at all.

## What the fork carries

A small patch set rides beyond upstream, each piece born from fleet use and each guarded by tests.
The developer-role fix maps Alibaba-bound developer prompts onto the accepted system role, so agent instructions survive the wire on both the Anthropic- and OpenAI-shaped paths.
The cache-first startup fix serves model catalogs from a fresh on-disk cache inside the four-hour TTL, returning pi launch to its no-extension baseline instead of blocking on a live fetch, while the live API stays the source of truth and the stale-cache fallback still works offline.
The tests pin both behaviors: request wiring for the role mapping, freshness logic for the cache fast path.

## Who it serves

It serves the captain's fleet first: unattended agents and the captain's own pi sessions running Qwen, DeepSeek, Kimi, GLM, and MiniMax models through the Plan subscription and Cloud keys.
It serves any pi user who hits the same two walls, because both fixes are strictly beneficial and change nothing for someone who never notices them.
It is not a channel to upstream and does not take feature requests: issues are disabled here, the fleet never posts to upstream, and upstream remains Francesco's project.

## What it owns, and what it refuses

The fork owns its patch set and the evidence that it works: the role mapping, the cache fast path, and the tests that guard them.
It refuses to fork upstream's purpose, provider model, auth shapes, or live-catalog philosophy; the fork stays a thin, fast-forwardable layer with no divergence in direction, only additions in payload.
It refuses hardcoded model lists, the one lesson upstream's changelog records painfully: fallbacks rot, live catalogs do not.
It owns nothing about credentials: tokens and keys stay in pi's own auth files under the user's home, never in the repo, never in logs.

## Principles for trade-offs

Fleet correctness beats upstream elegance: if Alibaba's wire rejects what agents send, the fork maps it, however awkward the upstream API shape.
Launch latency is a feature: agents start pi constantly, so one second of startup is a fleet-scale cost, and the cache is the fast path, not an afterthought.
Every carried fix earns a regression test before it ships, so a future sync from upstream cannot silently undo it.
Upstream compatibility beats local convenience: a fix that would make the next fast-forward painful is a fix shaped wrong.

## Non-goals

- Becoming the canonical distribution or a competing fork.
- Carrying features, models, or integrations the fleet does not use.
- Posting changes, issues, or PRs to upstream.
- Growing a second catalog, auth flow, or configuration surface beside upstream's.

## Done well in one year

The fork is still zero commits behind upstream, fast-forwarded the day the fleet notices a new release.
Its patch set is still a few small commits, each with a test, each either adopted upstream on its own merits or still carried cleanly.
A pi launch with the extension is indistinguishable in latency from a launch without it, and an agent's instructions always reach an Alibaba model intact.
Nobody in the fleet ever thinks about this fork, because nothing about Alibaba models in pi ever breaks.
