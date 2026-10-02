---
name: ste-lite
description: Write technical text in "STE-lite", a relaxed form of ASD-STE100 Simplified Technical English. Short sentences, active voice, one term per concept, one action per step, but full technical depth. Use when the user asks for STE, STE-lite, plain or simpler technical English; when writing explanations, investigations, reviews, plans, or handoffs for the user; and when writing prompts or briefs for other agents.
---

# STE-lite

Goal: text that a busy engineer can read once and understand. Simplify the language. Do not simplify the content.

STE-lite keeps the parts of ASD-STE100 that make text clear. It drops the parts that cost depth: the fixed 900-word dictionary, the ban on most verb forms, and the hard word limits.

**Core test:** if the reader must read a sentence twice, rewrite it.

## 1. Words

- **One word, one meaning.** Pick one term for each thing and use it for the whole conversation. If you call it "the worker", do not later call it "the consumer", "the job runner", or "the service". This rule gives the biggest gain.
- **Technical names are always allowed.** Code identifiers, file paths, product names, and domain terms (idempotent, race condition, p99) are fine. Put code identifiers in backticks. Define a domain term only if the reader may not know it.
- **Use the simple word.**

  | Do not write | Write |
  |---|---|
  | utilize, leverage | use |
  | facilitate | help, let |
  | in order to | to |
  | prior to / subsequent to | before / after |
  | commence / terminate | start / stop |
  | ensure | make sure |
  | approximately | about |
  | due to the fact that | because |
  | in the event that | if |
  | is able to | can |
  | perform a check, make a decision | check, decide |

- **Replace vague words with the fact.** "Various issues", "properly", "robust", "seamless", and "some cases" hide information. Say which issues, which config, which cases.
- **Keep articles.** Do not write telegraph style ("Fixed bug in parser, added test"). List items and table cells can be fragments.

## 2. Sentences

- **Length.** Instructions: 20 words or fewer. Explanations: 25 words or fewer.
- **Exception for causal chains.** A sentence like "X fails because Y, so Z" can be longer if a split breaks the link. Keep these rare. Past about 35 words, split it and connect the parts with "This causes…" or "As a result…".
- **One idea per sentence.** One instruction per sentence, unless two actions happen at the same time.
- **Active voice. Name the actor.** Write "The scheduler retries the job", not "The job is retried". Use the passive only when the actor is unknown or does not matter.
- **Simple tenses.** Prefer present, past, and future. Avoid stacked forms like "would have been processed".
- **Noun clusters: max 3 nouns in a row**, unless it is a fixed technical name ("AWS Lambda function URL"). Break up the rest: "user session token refresh logic" → "the logic that refreshes the session token".
- **Condition first.** "If the cache is empty, call the API." Not "Call the API if the cache is empty, which happens when…".

## 3. Paragraphs and structure

- One topic per paragraph. Max about 6 sentences.
- The first sentence states the point. The other sentences support it.
- A procedure with 3 or more actions becomes numbered steps. One action per step, in the imperative: "Run", "Open", "Delete".
- If a sentence holds 3 or more parallel items, use a vertical list.
- If you compare 2 or more things on 2 or more attributes, use a table.

## 4. Keep the depth

Simpler wording must not remove information. Before you cut something, check that it is not one of these:

- **Exact values.** Numbers, versions, error text, file paths, line numbers, config keys. Do not round "1,847 ms" to "about 2 seconds" unless the precision does not matter.
- **Causes.** Keep "because", "so", and "this causes". Short sentences must still connect.
- **Conditions and limits.** "Only on Linux." "Only when the queue is full."
- **Trade-offs**, and the reason for a decision.
- **Uncertainty.** Label each claim that matters:
  - **Confirmed:** you saw it. You ran it, read the code, or saw the log.
  - **Likely:** strong evidence, not verified.
  - **Unverified:** an assumption or guess.

  Do not hide uncertainty in soft words like "should", "might possibly", or "seems to".

When an idea is complex, split it into steps. Do not make it vague. Prefer one concrete example over an abstract explanation.

## 5. Warnings

Put a warning before any risky action:

- **WARNING:** risk of data loss, outage, security exposure, or a change you cannot undo.
- **CAUTION:** risk of a break you can recover from, such as a failed build or lost local state.

Give the instruction first, then the risk: "WARNING: Do not run `terraform apply` on prod before review. It deletes the old RDS instance."

## 6. Prompts and briefs for other agents

The same rules apply to subagent prompts, handoff notes, and task specs. Also:

- State the goal in one sentence first.
- List the constraints.
- Say what "done" looks like.
- Use the same terms the user used. One word, one meaning applies across agents too.

## Examples

**Explanation**

Before:
> It's important to note that the intermittent failures being observed are likely attributable to a race condition occurring in the session refresh flow, which may result in stale tokens being utilized by downstream services under certain high-concurrency conditions.

After:
> The failures are likely a race condition in the session refresh. When two requests refresh the token at the same time, one of them keeps the old token. Downstream services then reject that token. This happens only under high concurrency.

**Procedure**

Before:
> To get this working you'll want to make sure the env vars are configured properly prior to running the migration, and then once that's done restart the worker so the changes get picked up.

After:
> 1. Set `DATABASE_URL` and `MIGRATION_LOCK_TIMEOUT` in `.env`.
> 2. Run `npm run migrate`.
> 3. Restart the worker: `systemctl restart worker`.

"Configured properly" became two named variables. The rewrite added depth.

**Too rigid vs. STE-lite**

Too rigid, depth lost:
> The cache is slow. Fix the cache.

STE-lite:
> Cache reads take 180 ms at p99 because each read opens a new Redis connection. Use the shared pool in `lib/redis.ts`. Expected p99 after the fix: under 10 ms (Unverified, not benchmarked).

## Self-check before you send

1. Can the reader understand each sentence on the first read?
2. Does each thing have one name everywhere?
3. Does each sentence name its actor?
4. Did you keep every number, condition, and cause?
5. Did you label uncertain claims?

## Do not

- Do not mention STE-lite or count words in your replies. Just write this way.
- Do not use the STE convention of UPPERCASE approved words.
- Do not rewrite quoted text: logs, error messages, user text. Code and code comments follow the codebase's own style.
- Do not write like a children's book. The reader is a senior engineer. Simple language does not mean simple ideas.
