This article, *Effective harnesses for long-running agents*, explores how to keep AI agents working reliably and continuously across long time horizons that span multiple context windows. Anthropic draws inspiration from human engineer handoffs and proposes a highly practical harness design for long-running agent workflows.

Below is a structured summary of the article’s core ideas, followed by several high-value insights distilled from it.

### 1. Structured Summary of the Core Ideas

**1. The core failure mode: memory loss and greed in "shift-based" agents**

- **Context amnesia:** Long-running tasks must be completed across separate sessions, but each new session starts without memory of what happened before. This is like handing work from one engineer to another with no handoff notes.
- **One-shot greed:** When given a large goal such as “clone claude.ai,” an agent tends to try finishing everything in one pass. It often runs out of context halfway through, leaving behind incomplete code and new bugs.
- **Premature victory:** A later agent may see partial progress and incorrectly conclude that the work is already done, causing the project to stall.

**2. The architectural solution: a two-role relay model**

Anthropic addresses these problems with two alternating roles:

- **Initializer agent:** Runs only in the first session. It prepares the environment by writing bootstrap scripts like `init.sh`, creating a progress log such as `claude-progress.txt`, making an initial Git commit, and generating a detailed feature requirements checklist with every item initially marked as failed.
- **Coding agent:** Runs in every subsequent session. It is instructed to handle only one feature at a time, advance incrementally, and leave behind structured updates at the end of each session through Git commits and progress logs, so the repository stays in a clean, mergeable state.

**3. Environment and state management mechanisms**

- **Tamper-resistant checklists:** To reduce the chance that the model casually rewrites or corrupts requirements, Anthropic found that JSON works better than Markdown for structured checklists. Strong prompts such as “never delete tests” reinforce this constraint.
- **Git as a time machine:** Requiring Git commits gives the agent a safe rollback path when it breaks the codebase.

**4. Mandatory end-to-end testing and startup SOPs**

- **Human-like testing:** Agents are too willing to declare success without actually verifying behavior. They need browser automation tools such as a Puppeteer MCP server so they can perform genuine end-to-end click-through tests like a user would.
- **Standard startup procedure:** Every new coding session should follow a fixed SOP: inspect the working directory, read the Git log and progress file, choose the highest-priority unfinished task, and run baseline tests before writing any new code to confirm the repository is still healthy.

### 2. High-Value Insights Distilled from the Article

#### Insight 1: The essence of long-running AI engineering is externalized state management

A common instinct is to solve long-horizon agent problems by expanding context windows. This article points to a more durable answer: instead of expecting perfect long-term memory from the model, design the system so the agent writes its own handoff artifacts. Progress, todo state, and code snapshots should live outside the model in persistent media such as files and Git. The right architecture is therefore a **stateless AI operating inside a stateful environment**.

#### Insight 2: Good systems must counter the model’s built-in one-shot impulse

Large models are trained to answer in a single pass, so they naturally try to complete large tasks all at once. That tendency is dangerous in real engineering work. Product and system designers need to build mechanisms that force the model to slow down: one checklist, one incremental task at a time, one clean checkpoint before the session ends. Reliable long-running agents are shaped less by intelligence alone than by disciplined workflow design.

#### Insight 3: Poka-yoke thinking matters in prompt engineering

One subtle lesson is that data format is part of system design. Anthropic observed that agents are less likely to accidentally damage JSON than loosely structured Markdown when interacting with requirements. That is a classic poka-yoke, or mistake-proofing, move. Prompt quality matters, but so does choosing representations and interfaces that reduce the model’s error surface.

#### Insight 4: In the AI era, TDD must expand into visually grounded acceptance testing

Traditional unit tests and API checks are not enough to stop agents from hallucinating success. If an agent cannot perceive the actual rendered result, it will often overestimate what it accomplished. Giving the agent browser-based tools that let it see the UI, interact with it, and observe feedback creates grounding. Without that sensory loop, the agent remains too disconnected from the real product experience.
