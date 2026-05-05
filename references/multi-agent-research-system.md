# How We Built Our Multi-Agent Research System

This article details the Anthropic team's real-world engineering experience in building and pushing a multi-agent research system from zero to production. Unlike papers that stay at the theoretical level, this article directly addresses the architecture, evaluation, tool design, and engineering challenges during agent implementation.

## I. Structural Overview of Core Viewpoints

### 1. Architecture Choice & Core Logic: Why Choose Multi-Agent?

-   **Response to Dynamic & Open Problems**: Research work is inherently open-ended and cannot be hard-coded into fixed paths. Multi-agent systems can perfectly meet this demand through parallel exploration and dynamic adjustment.
-   **"Orchestrator-Worker" Pattern**: The system employs one "Lead Researcher" (Lead Agent) paired with multiple "Subagents." The Lead Agent analyzes queries, formulates strategies, and assigns tasks; Subagents handle parallel retrieval, filter info, and return results, with a dedicated "Citation Agent" handling source attribution.
-   **Token Scalability in Practice**: Anthropic found that for difficult-to-obtain information, **80% of performance differences could be explained solely by token consumption**. Multi-agent architecture is an effective means of breaking context window limitations and significantly increasing token throughput through parallel reasoning. However, the token consumption of a multi-agent system is usually about 15 times that of a common conversation.

### 2. Best Practices for Prompt Engineering & Tool Design

-   **Teaching the Lead Agent "How to Lead"**: If commands are vague, Subagents will perform a lot of redundant work (e.g., several agents searching for the same question). Clear objective goals, output formats, tool guidelines, and task boundaries must be explicitly given in the prompt.
-   **Scaling Effort Based on Need**: Simple information retrieval may only need one Subagent, while complex comparisons or deep research may need 10+. "Scallability rules" must be embedded in the prompt to prevent agents from over-consuming resources on simple questions.
-   **Reinforcing the Thinking Process**: Using an "Extended Thinking Mode" as a controllable scratchpad allows agents to plan before tool calls and evaluate quality/identify gaps afterward, which significantly improves instruction following and reasoning efficiency.
-   **Parallelism is the Key to Speed**: The Lead Agent starting multiple Subagents simultaneously, plus Subagents calling multiple tools at once—this double parallelism shortens research time for complex queries by 90%.

### 3. Evals: Moving Away from "Step-by-Step" Compliance

-   **Start Small and Iterate Fast**: Minor prompt changes could lead to success rate leaps (from 30% to 80%), so don't wait for hundreds of test cases before starting evaluation; start from around 20 real-world queries.
-   **LLM-as-a-Judge**: Research outputs are mostly long text, which is hard to verify with code. By setting up scoring scales containing factual accuracy, citation correctness, and completeness, an LLM as a judge gives 0.0-1.0 scores and pass/fail ratings, high-aligned with human judgment.
-   **Human Evaluation is Irreplaceable**: Automated evaluations easily miss edge cases. For instance, early agents tended to cite "SEO content farms" rather than authoritative but lower-ranking academic PDFs, prompting teams to add heuristic rules for source quality in the prompt.
-   **Focus on "End-State Evaluation"**: Multi-agent systems are non-deterministic; different agents might reach goals through completely different valid paths. Therefore, don't grade an agent's intermediate steps; evaluate whether the final state delivered is correct.

### 4. "Last Mile" Engineering Challenges in Production

-   **State Management & Error Accumulation**: Agents are long-running and stateful; a small error (e.g., failed tool call) can cause a butterfly effect, leading the task to completely deviate. Systems must support "resume-from-error-breakpoint" mechanisms, combining deterministic retry logic with model self-correction.
-   **Observability**: Debugging non-deterministic systems is extremely difficult. End-to-end tracing and monitoring of agent decision patterns and interaction structures are mandatory to find the root cause of failure without invading user privacy.
-   **Rainbow Deployments**: Because agents may be at any stage of a long-running task, code updates shouldn't be a simple cut-off; gradual/rainbow deployments must be used to prevent interrupting an agent in the middle of a task.

## II. High-Value Insights Based on the Article

### Insight 1: "Might is Right" in the AI Era is "Token Throughput via Parallelism"

The article reveals a blunt data point: **Token consumption explains 80% of the effect variance.** This means the core secret of current multi-agent systems beating single agents doesn't lie in how sophisticated the system logic is, but in how it violently breaks through the single-model compute and context bottlenecks via parallelism. For product managers, this means ROI calculation is key—only high-value business flows (e.g., medical decisions, complex competitive research) are worth adopting a 15-fold token increase.

### Insight 2: ACI (Agent-Computer Interface) Needs to be Treated as an Independent Discipline

Tool calling is the only way an agent interacts with the world. Anthropic found that vague tool descriptions lead agents astray. More inspiringly, they created a **"Tool-testing Agent"**: letting AI try out flawed tools and automatically rewrite tool descriptions (Docstrings) after hitting a snag to prevent later agents from making mistakes, which reduced task completion time by 40%. This means future API and tool documentation should not only be written for human developers but also need "reverse adaptation and optimization" through AI.

### Insight 3: Give Up Process Control; Embrace "Acceptance Based on Intent and Results"

Traditional software engineering emphasizes use cases and deterministic execution paths. But in multi-agent systems, tiny state changes catalyze emergent behaviors. If managers or QA teams still try to "verify if the agent completed the task in 5 preset steps," they will fall into endless Debugging hell. System management philosophy must shift from "controlling the process" to "managing the end-state." As long as the final output passes multi-dimensional LLM-judge cross-validation, intermediate detours are allowed system redundancies.

### Insight 4: External Storage and "File Systems" are the Optimal Solutions to Context Disasters

As conversation turns increase, agents easily suffer from "context overflow" or hallucinations. Anthropic proposed two practical solutions: first, letting agents regularly summarize milestone achievements and **save them to external memory**, only passing summaries (not the whole history) to new Subagents; second, letting Subagents **directly write large outputs into an external file system**, only passing "file links/references" back to the Lead Agent. This avoids the "Game of Telephone" effect or token waste when passing long text between multiple agents, and is a must-have pattern for building robust enterprise AI systems.
