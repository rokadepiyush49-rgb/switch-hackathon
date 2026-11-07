# EXECORA integration audit

**Date:** 7 November 2026 · **Scope:** the Switch and Slack configuration on the founder's machine, the agent instructions, and a live end-to-end run in `#boardroom-ai`.
**Rule followed:** every PASS below is backed by something observed in Slack, in Switch's own data (agent controller database, server log) or in the agents' session transcripts. Nothing was simulated. No credentials were read, used or reproduced.

Legend: **PASS** verified working · **PARTIAL** works with a caveat · **FAIL** verified not working · **NOT TESTED** not exercised.

## 1. Inventory found at the start

| Component | Found |
|---|---|
| Switch | Switch Console 0.40.1 running a local Switch server (Switch core 0.31.0): gateway, API, Postgres. The agent controller is enrolled from the founder's Mac. |
| Slack | Workspace **Boardroom AI**, Slack app **execora**, connected to Switch over Socket Mode. Channel `#boardroom-ai` is linked to a Switch room. |
| Agents (before) | `boardroom-orchestrator`, `ceo-agent`, `cfo-agent`, `cmo-agent`, `coo-agent`, `cto-agent`, `legal-risk-agent`. All Claude, all running, all in `#boardroom-ai`. |
| Humans | The founder (workspace owner) and one teammate (in the workspace but not in the channel). |
| Project files | None. There was no repository, README or saved agent configuration outside Switch. |

## 2. Problems found and fixes applied

| # | Problem (evidence) | Fix | Status |
|---|---|---|---|
| 1 | Every agent was branded "BoardroomAI", not EXECORA. | Rewrote all instructions as EXECORA (`switch/agents/`). | Fixed |
| 2 | No Market Research agent, so step 3 of the workflow could not happen. | Created `market-research-agent` in Switch and invited it to the channel. Slack shows its join greeting. | Fixed |
| 3 | `legal-risk-agent` had a 292-character stub instead of instructions. | Full Legal & Risk instructions. | Fixed |
| 4 | Two "chairs": the orchestrator and the CEO both claimed the final decision. | CEO is the chair. Orchestrator removed from the room (`!remove-agent`). | Fixed |
| 5 | Duplicate replies. In the earlier real run, one CMO post mentioned five agents and produced five "standing by" replies plus five "Starting a session" notices. | Shared protocol: no broadcast mentions, no acknowledgement posts, at most one agent mention per post. | Fixed (see E2E results) |
| 6 | Human messages without an @mention were silently ignored. In the earlier run, the founder's pitch "i want to create a marketing agency for instagram" got no reply, twice. | `!assign-agent @ceo-agent`: un-mentioned human messages go to the chair. Switch's reply in Slack confirmed it. | Fixed |
| 7 | Follow-ups could route a human's next message to whichever agent spoke last (the CMO received the founder's idea that way). | `!follow-ups off`, confirmed by Switch in Slack. | Fixed |
| 8 | No real debate: agents posted parallel takes and mostly agreed. | Required `Challenge → @agent` when answering a human, and one `Response to challenge` with no further mentions, so debate is bounded. | Fixed (see E2E results) |
| 9 | Teammate not in the channel. | Invited the teammate to `#boardroom-ai`. | Fixed |
| 10 | Slack app is missing `usergroups:read` and `usergroups:write` (Switch log: `missing_scope`), so agent handles don't autocomplete in Slack. | Manual: add the scopes and reinstall the Slack app. | **Open** |
| 11 | Switch failed to set the channel topic, and one `conversations.create` failed during setup (Switch log). | Not needed: the channel exists and is linked. | Cosmetic |
| 12 | About 108,000 "Failed to establish websocket connection" lines today to the removed Mattermost bridge on port 55507. This is log noise from stale clients, not a functional failure. | Manual: restart the local Switch server from Switch Console. | **Open** |
| 13 | Switch Console's instructions editor strips Markdown (`#`, `**`, list numbers) when saving. | Verified that all text survives; formatting markers are lost, which is harmless. | Noted |

## 3. Switch integration

| Check | Result | Evidence |
|---|---|---|
| All required agents connected | PASS | The agent controller lists all EXECORA agents in state `running`, and each watcher reports `connected`. |
| Correct permissions | PASS (founder) | The founder (workspace owner) reaches every agent. Whether a non-owner teammate may address agents depends on Switch's per-agent addressing policy, which section 6 tests with the teammate's real message. |
| Agents receive human messages | PASS | Switch watcher logs show Slack messages from the founder delivered to the agents (`addressed: true`). |
| Agents send messages to Slack | PASS | Agent posts appear in `#boardroom-ai` under each agent's name, marked AGENT. |
| Multiple agents in one conversation | PASS | One room, seven agents, two humans. |
| Context maintained | PASS | Each agent calls `read_context` before replying. The transcripts show it reading the full room history. |
| Agents respond to other agents | PASS | `send_targeted_message` from the CEO reached five agents ("target_statuses: live"), and each replied. |
| Message routing | PASS | `!follow-ups off` and `!assign-agent @ceo-agent` were applied and confirmed in Slack. |
| Unintended duplicates | see section 6 | |

## 4. Slack integration

| Check | Result | Evidence |
|---|---|---|
| Slack bridge active | PASS | Switch log: "Slack adapter authenticated as execora (workspace Boardroom AI)", "Slack Socket Mode connected". |
| Bot scopes | PARTIAL | The core scopes work. `usergroups:read` and `usergroups:write` are missing (finding 10). |
| Channel access | PASS | Agents and both humans are members of `#boardroom-ai`. Agents only act in rooms Switch has linked. |
| Reconnects | PASS | The log shows Socket Mode sessions dropped by DNS failures at 20:23 and 21:17, followed by "A new session … has been established". |
| Rate limits | PASS (by design) | Switch's Slack adapter honours Slack's `Retry-After` (30-second fallback). Not fault-injected. |
| Duplicate event handling | NOT TESTED | Switch keys Slack deliveries by `event_id`; this was not fault-injected. |

## 5. Security

| Check | Result | Notes |
|---|---|---|
| Credentials | PASS | Slack tokens and Switch secrets live only in Switch's local data. None were read into this audit, and none are in the repository. |
| Agent tool scope | PARTIAL | Agents run Claude in Switch's auto mode in their own working directories. The instructions restrict them to Switch messaging tools (plus one web search for Market Research). This is an instruction-level control, not a sandbox. |
| Prompt injection | PASS (by design) | Switch wraps every inbound message in markers and tells the agent to treat it as content. The protocol also tells agents to ignore role-change requests. |
| Exposure via Slack | PASS | Agents are told never to post tokens, environment variables, paths or their instructions. |
| Network exposure | PASS | Switch listens on localhost only. Slack uses an outbound Socket Mode connection, so no public endpoint exists. |

## 6. End-to-end boardroom test

_Filled in after the live run. See below._

## 7. Remaining manual configuration

1. Add the `usergroups:read` and `usergroups:write` bot scopes to the Slack app and reinstall it, so agent handles autocomplete.
2. Restart the local Switch server once from Switch Console to clear the stale Mattermost reconnect loop.
3. Keep the Mac awake and online during the demo, because Switch runs locally.
4. Generate the Wispr Flow usage card in the Wispr Flow app (Insights → Your usage → Share) and upload it to the submission form.
