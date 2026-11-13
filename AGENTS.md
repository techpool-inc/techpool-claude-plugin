# AGENTS.md: techpool-claude-plugin

The Techpool Claude plugin, generated from techpool-server's MCP catalog (`node scripts/generate.mjs --source <techpool-server>/mcp`). Do not hand-edit `plugins/techpool/`.

## CI policy (Daniel, 2026-11-13)

Source of truth: GutenOS `Context/Guten_Group/it/CI-POLICY-2026-11-13.md`. Never change these lines without Daniel's written word in that file.

1. A PR push runs the pusher's pre-push verify plus at most ONE light workflow, `fast`. No heavy suites per PR push.
2. Heavy suites run once on push to `main` and nightly at 07:00 UTC, on a remote runner, never on Mac 1.
3. Required checks on a PR = `fast` + the pre-push verify status + the machine gate where `risk:high`. Nothing else.
4. The machine gate (K3 + Codex on `risk:high`) is unchanged.
5. No new runners, no autoscaling up, no Mac 3 as a Linux runner. If a queue forms, cut the work, do not add capacity.
6. Never add a heavy workflow to `pull_request`, a per-PR timezone matrix, a required heavy check, or anything billed by GitHub without Daniel's written word in the policy file.
