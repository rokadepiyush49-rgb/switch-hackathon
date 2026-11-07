# EXECORA: three-minute demo script

Everything below happens in the real Slack channel `#boardroom-ai`. Nothing is pre-recorded or simulated: each message is typed (or spoken with Wispr Flow) by a real person, and every agent reply comes live from Switch.

**People:** the founder and a teammate, each signed in to their own Slack account.
**Before you start:** run the pre-flight checks below about 10 minutes before recording.

## Pre-flight (not recorded)

1. Switch Console is open and the local Switch server shows as running.
2. In `#boardroom-ai`, post `!agents-status`. The CEO, market research, CTO, CFO, CMO, COO and legal agents must all be listed as online.
3. Warm the agents up so the first reply is fast: post `!agents-greet`, then delete the greeting messages if you want a clean channel.
4. Open Wispr Flow on both laptops and check that the dictation hotkey types into the Slack message box.

## Run of show

| Time | Who | What happens |
|---|---|---|
| 0:00 | Narrator | "EXECORA is an AI boardroom that lives in Slack. Every executive is a real AI agent running on Switch, and humans sit at the same table." |
| 0:10 | Founder | Posts message 1, the idea, using Wispr Flow voice dictation. |
| 0:20–0:50 | CEO Agent | Assesses the business opportunity and convenes the board with one tailored question each for Market Research, the CTO and the CFO (plus Legal if it decides student data needs it). |
| 0:50–1:30 | Market Research, CTO, CFO | Each posts its own analysis: competitors, technical feasibility and monetisation. |
| 1:30 | Teammate | The teammate posts message 2, the hard question, from their own Slack account. |
| 1:40–2:15 | CFO and Market Research | Each answers the teammate directly, then ends with `Challenge → @…` aimed at another executive's assumption. |
| 2:15–2:30 | Challenged agents | Each replies once with `Response to challenge —`, conceding or holding its ground. |
| 2:30 | Founder | Posts message 3 to close the meeting. |
| 2:35–3:00 | CEO Agent | Posts the EXECORA Board Resolution: decision, disagreements and how they were resolved, risks, a 30-day plan and kill criteria. |

## Messages to post

**Message 1: founder**

```text
@ceo-agent New idea for the board: An AI-powered platform connecting college students with internship opportunities. Please evaluate the opportunity and convene the board.
```

**Message 2: teammate**

```text
@cfo-agent @market-research-agent Hard question: students won't pay for this, and LinkedIn, Internshala and Handshake already list internships for free. Who actually pays us, and why would a company or a college switch to us?
```

**Message 3: founder**

```text
@ceo-agent Please close the meeting with the board's final recommendation.
```

## Tips

- Type agent handles exactly (`@ceo-agent`). Slack will not autocomplete them until the Slack app has the `usergroups:read` and `usergroups:write` scopes (see the README).
- A message with no @mention goes to the CEO Agent, because the room's main agent is the CEO (`!assign-agent @ceo-agent`).
- The first time an agent wakes up, Switch posts "Starting a session to handle this — one moment." That is normal.
- If an agent stops replying, post `!agents-status`. If it shows as offline, restart it from Switch Console, or post `!reset @agent-name`.
