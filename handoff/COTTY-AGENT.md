# Handoff: cotty agent → tylerbroqs/cotty

`cotty-agent.patch` is one commit for https://github.com/tylerbroqs/cotty,
made on top of `f236021` (feat(desk): Lighter perp basis and funding carry).
It could not be pushed from the session that wrote it (no push access to
that repo).

## Apply it

From a clone of `tylerbroqs/cotty`:

```sh
git checkout main && git pull
git am /path/to/cotty-agent.patch     # keeps the original commit message
go build ./... && go vet ./... && go test ./...
git push
```

If `main` moved and `git am` conflicts, run `git am --3way` and resolve.

## What it adds

- `cotty agent [flags] <join-url>` — an AI model joins the session as a guest
  named `agent`: reads the shared screen, answers questions, view-only until
  `cotty ctl allow agent`, then types one command line per step and reports
  back. `deny` / `kick` work as for people; the audit trail logs its
  keystrokes (`"who":"agent"`) plus `ask` and `say` events.
- `cotty ask <question>` — run inside the hosted shell; waits for the agent's
  answer and prints it in the shell (everyone sees it, recordings keep it).
- Providers: `-provider anthropic` (default, `claude-opus-5-5`, Messages API,
  `ANTHROPIC_API_KEY`) or `-provider openai` for any OpenAI-compatible
  endpoint (`-model` required, `-base-url`, `-key-env`).
- Protocol: new `ask` and `say` frames, sealed like output in end-to-end
  encrypted sessions; relays forward them and attribute `say`. `say` is rate
  limited per sender (5 per 10s).
- Fix: `cotty ctl list` through a relay no longer runs all guests onto one
  line (control results now use `SanitizeLines`, which keeps newlines).
- README section "AI agent: an agent on a leash".

## How it was verified

- `go vet ./...`, `go test ./...` (new tests in `internal/agent`,
  `internal/host`, `internal/protocol`), `go test -race` on those packages.
- End-to-end with real PTYs against a scripted fake OpenAI-compatible model:
  relay + encrypted host, local host, a CLI guest and a browser guest. Checked:
  answers printed in the shared shell, view-only agent only proposes,
  allowed agent runs the command and reports back, `ctl list` one line per
  guest, audit attribution.
- **Not yet verified against the real Anthropic or OpenAI APIs** (no API key
  in that session). Do one real run before announcing:

```sh
cotty host -audit trail.jsonl          # terminal 1
ANTHROPIC_API_KEY=... cotty agent "ws://localhost:7373/ws?code=CODE"   # terminal 2
cotty ask "what is in this directory?" # inside the hosted shell
```
