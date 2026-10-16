# Episode-51: Top rules for agentic development

Carl and Rocky discuss their top rules for agentic development

📺 YouTube video: https://youtu.be/

🏠 Code it with AI Home Page: https://codeitwithai.com

## Carl's Rules

### Rules for Programming with AI

##### 1. Pick a language you are fluent in

You might be tempted to let the agent pick the language. If you do that you have no way to check the work, and that, as you will see, can lead down a dark path.

##### 2. Comments and documentation

Require the agent to comment all code it writes. Also, as new features are added, the agent should update a customer-facing document explaining how the software works. Another document should be created for the end-user, a user manual. Do it as the code is developed and it won't take so much time afterwards.

##### 3. Small iterations

Rocky says this too. My workflow is to plan big up front. Have the agent break the work into phases, and each phase into steps. Those steps will become individual commits. Before committing, read the code, run the app, and approve the commit. Rocky likes to have the agent commit locally and he approves the pushes, but I prefer to be in charge of all commits. I also ask the agent to come up with a one-paragraph commit message with no formatting. Plain text.

## Rocky's Rules

### Rules for Programming with AI

Lessons from a couple of years of building software with Claude Code and GitHub Copilot — on RockBot, CSLA .NET, the MCP aggregator, and many other projects — after moving from AI skeptic to AI pragmatist to not writing code by hand at all.

---

#### The Top 5

##### 1. Be a systems thinker, not a typist

The value a developer provides was never typing speed or memorizing language syntax. It is the ability to decompose a problem, design a system that is maintainable, scalable, and resilient, and navigate the trade-offs between users, business goals, and technical constraints.

AI now does the typing. It also compresses everything else: code, tests, docs, deployment scripts, and pipelines come together in parallel, often in a day instead of weeks. That means you use systems thinking *more* than before, not less, because the cycle from idea to implementation is so short that you have to hold the whole system in your head all day long.

Treat the AI as a collaborator — say "we" — but remember you are the architect. Without real architectural judgment, the output is architecturally naive. With it, the AI amplifies decades of experience.

**The question for every developer:** are you a systems thinker, or are you a typist? AI already types faster than any of us.

##### 2. The spec is the bottleneck

Vague prompts produce vague results. "Make the login page better" is a wish, not a spec. Most rework traces back to something you didn't say, not something the AI got wrong. And because generation isn't deterministic, the same vague prompt can give you different results every time.

Give the AI the context a sharp contractor would need. A template that works:

1. **Who am I?** My role and experience.
2. **Who are you?** The role the AI should play.
3. **Who is the end user?** Who this is for and what they need.
4. **What are we building?**
5. **Why are we building it?**
6. **How are we building it?** Stack, frameworks, patterns.
7. **What are the constraints?** Security, scale, performance, cost.

Two refinements:

- **Let the AI draft the spec.** Describe what you want in a few sentences and have the agent write the spec. It usually catches edge cases and acceptance criteria you wouldn't have bothered to write down. Editing a draft is much faster than a blank page, and the agent then works from a spec it helped create.
- **Put non-functional requirements in the spec.** If performance, memory, bandwidth, startup time, or cost matter, make them explicit targets. If they aren't in the spec, don't be surprised when the agent ignores them.

##### 3. Close the loop

The big productivity gain doesn't come from how fast the agent writes code. It comes from whether the agent can **verify its own work** — build, run unit and integration tests, read logs and traces, and iterate — without waiting for you. That turns iterations from hours into minutes.

Whether the loop can be closed is mostly decided before the agent writes a line of code. It needs:

- **A programmable interface**, preferably a CLI that exercises the same code paths as the UI. If the only way to test is a human clicking through screens, the agent is stuck.
- **Structured logs** with real context: what was called, what failed, and what state the system was in. Logging has become the agent's debugger.
- **OpenTelemetry traces** for anything distributed, so the agent can see *where* a failure happened, not just *that* it happened. Aspire gets a .NET app most of the way there for free.

You don't have to build these yourself. Put them in the spec and the agent will build the tools it needs to close its own loop.

This matters most where "don't read the code" breaks down: concurrency and state. A threading race condition took a long time to resolve with logs pasted back and forth, and was only found once heavy instrumentation made it visible. For subtle bugs, have the agent instrument first and theorize second.

The inner loop — build, test, observe, iterate — belongs to the agent. The outer loop — does this actually solve the problem, and does it feel right in the real UI — belongs to the human.

##### 4. Tight iterations, not marathons

Long-running autonomous agents are a non-goal. Work in iterations of roughly 10 to 90 minutes between human check-ins.

The longer an agent runs unsupervised, the more likely it is to drift: it makes a wrong assumption early, builds on top of it, and ends up somewhere you didn't want to go. Caught after ten minutes, that's a short conversation. Caught after four hours, it's a revert.

Short iterations also keep *you* engaged with the system. If you're the systems thinker in the collaboration, you need to actually be thinking about the system, not checking in occasionally on an agent that has been off doing who-knows-what.

A workflow that works well with plain Claude Code, no scaffolding plugins required:

1. **Ideate** — new feature, bug fix, troubleshooting.
2. **Discuss** — options, scope, research into the existing code.
3. **Plan** — in plan mode, and *record the plan in the repo*.
4. **Implement** — the plan or one phase of it, ending in a PR.
5. **Test** — unit and integration; repeat 4–5 as needed.
6. **Merge** — then back to step 1 or 3.

##### 5. Review with different eyes — AI and human

A model reviewing its own code shares its own blind spots. Have a *different* tool review the work — Copilot reviewing Claude Code's PR, for example. Different training and different defaults catch a different class of mistakes. Feed the review comments back to the authoring agent and let it fix or push back; the places where the two disagree are where a human should look. Point reviewers at specific concerns (concurrency, security, framework patterns) rather than asking for a generic review.

Then get a human review, because humans catch something else entirely. On [CSLA PR #4925](https://github.com/MarimerLLC/csla/pull/4925), a large Claude Code–authored PR, the human reviewer's feedback was almost entirely things no AI reviewer would have caught:

- The solution wouldn't pack on his Linux machine.
- Whether a change really belonged under "breaking changes" in the release notes.
- Confusing wording about default settings.
- A real-world constraint — his team must use async-only APIs — that led to new configuration options.
- A Roslyn best practice: diagnostics belong in analyzers, not source generators.
- A question that surfaced a separate, preexisting bug.

AI reviewers are good at code-level defects. Humans bring the consumer's perspective: API ergonomics, documentation, real environments, ecosystem expertise, and "I tried it and it broke."

---

#### Appendix: More Rules

##### Mindset

**Re-evaluate constantly.** Models and tools change monthly. If you decided AI couldn't handle something three months ago, it may handle it now. The reverse applies too: workflow plugins that once provided essential structure can become redundant as the tools mature, so try going without them. Match models to tasks, and recheck your setup — one machine stayed stuck on an older model for weeks because of a pinned config setting.

**Stop writing code, not reading it.** An early experiment in deliberately never reading the generated code worked surprisingly well — until it didn't. You can give up writing code entirely, but keep reviewing PRs and watching what the agent does, so you can keep it focused and on track.

##### Context and Knowledge

**Write for the AI, not a human reader.** Docs written for humans explain jargon, expand acronyms, and use narrative flow to keep readers engaged. An AI doesn't need any of that, and every extra token consumes context window. Be concise and dense. This applies to CLAUDE.md, plans, skills, and MCP server content.

**Ground the AI in authoritative, versioned knowledge.** Models are trained on every version of your framework at once — CSLA 1.0 through current — so they confidently produce outdated code. An MCP server with version-specific docs made the results *substantially* better. Also, trust schemas over prose: tool calls failed repeatedly when parameter names were inferred from descriptions instead of read from the actual `inputSchema`.

**Record plans and state in the repo.** Sessions die — usage limits, `/clear`, crashes. Plans kept as markdown files in the project are durable artifacts you can point a fresh session at. This one habit replaces most of what workflow plugins provide.

**Teach the next instance.** When you solve a tooling problem, write the fix where the next agent session will read it — a skill doc, CLAUDE.md, or a note in the plan. Otherwise every new session rediscovers the same failure.

##### Workflow

**Humans have a context window too.** Two concurrent agent sessions is a realistic limit. Beyond that, you become the bug: forgetting context, giving one agent instructions meant for another. Parallelize only work that is truly isolated — doc updates, dependency bumps, fixes in unrelated modules — and hand those to another window or a cloud agent.

**Commit like save games.** Make a local commit at every milestone, however small: code compiles, tests pass, a step works. When the AI goes off the rails, you're minutes from a known good state instead of hours. Land sweeping mechanical changes, like renames, as their own commits so you can bisect later.

**Use AI to build tools, not to be the tool.** For repetitive tasks, have the AI write a script once rather than spending tokens on the same work over and over. This also applies to exhaustive queries: asking the AI to list every class implementing an interface across a large codebase gave results that were wrong in both directions. Asking it to write the grep gives the right answer every time.

**Verify your customizations actually fire.** A custom CSLA sub-agent was defined and carefully configured — and the general-purpose agent kept writing the code instead. Don't assume your agents, skills, and tools are being used. Check.

**Say whether you're asking or requesting.** In agent mode, a question can be interpreted as a work order. Be explicit when you want an answer rather than an action, and be direct when you do want an action — "do X," not "it would be great if you could…"

**Keep AI-generated PRs human-reviewable.** AI makes huge PRs cheap to produce, but human attention doesn't scale with them. When a reviewer has to skim, most of the value of human review is lost. Have the agent split work into reviewable pieces, even when it could do everything in one shot.

##### Verification

**Let the agent break things somewhere safe.** An agent running its own loop needs the freedom to fail aggressively. Give it disposable containers or a dev namespace, test identities, least-privilege access, and mocked or sandboxed dependencies — so whatever it breaks doesn't matter.

**Enforce with code; suggest with prompts.** Instructions in CLAUDE.md or a prompt — even with "always" and "never" — are requests, and a non-deterministic model will sometimes ignore them. Compilers, tests, analyzers, and hooks are guarantees. When something must always be true, make it a check, not an instruction.

**Mechanical sweeps are easy; semantic residue isn't.** A large rename caught every code symbol, but strays hide in prompt fragments, log message templates, configuration, and comments that refer to a concept rather than an identifier. After a sweeping change, exercise the code paths that use the renamed concept and look for leftovers.

### Links:

* https://blog.lhotka.net/2026/04/23/My-Claude-Code-Workflow
* https://blog.lhotka.net/2026/09/25/Closing-The-Loop
* https://blog.lhotka.net/2026/04/02/Systems-Thinking
* https://blog.lhotka.net/2026/01/11/Writing-Docs-For-An-Ai
* https://blog.lhotka.net/2025/11/25/AI-Skeptic-to-AI-Pragmatist
