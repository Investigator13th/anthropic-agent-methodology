---
name: anthropic-agent-methodology
description: Full-stack methodology for building AI agents based on Anthropic's research. Covers architectural patterns (Workflow vs. Agent), domain-specific skill injection, multi-agent orchestration, and production-grade evaluation. Use when designing complex, scalable, and specialized agentic systems.
---

# Anthropic Agent Methodology

This skill is synthesized from Anthropic's core practical guides, providing a comprehensive path to building efficient, reliable, and scalable agentic systems.

## I. Architectural Decisions (Workflow vs. Agent)

Decide on the appropriate architecture based on the task's openness:

-   **Workflows**: Best for well-defined tasks with clear boundaries. Provides high predictability and consistency.
-   **Agents**: Best for open-ended problems where paths cannot be hard-coded. Offers high flexibility but carries architectural risks like error accumulation.

### Core Composable Patterns
1.  **Prompt Chaining**: Breaking tasks into sequential sub-tasks.
2.  **Routing**: Classifying inputs and directing them to specialized downstream tasks or models.
3.  **Parallelization**: Includes **Sectioning** (parallel sub-tasks) and **Voting** (redundancy for confidence).
4.  **Orchestrator-Workers**: Dynamic task decomposition and distribution by a lead model.
5.  **Evaluator-Optimizer**: An iterative loop of generation and evaluation for quality refinement.

## II. Skill-Driven Evolution (Skills Library)

Instead of building isolated vertical agents, inject specialized "Skills" into a general-purpose agent.

-   **What is a Skill?**: Domain expertise, SOPs, and best practices packaged as files (Markdown or scripts).
-   **Progressive Disclosure**: A three-level loading mechanism (Metadata -> SKILL.md -> References) to balance depth with context efficiency.
-   **Code as Tools**: Encouraging agents to write, modify, and persist scripts as their own permanent tools.

## III. Multi-Agent Orchestration & Scale

Leverage token throughput through parallelism to bypass single-model limitations.

-   **Lead-Subagent Architecture**: A Lead Agent manages strategy and orchestrates multiple Subagents for parallel execution.
-   **ACI (Agent-Computer Interface) Design**:
    -   Enforce **absolute paths** to minimize reasoning errors.
    -   Prefer **Markdown** over complex JSON or calculation-heavy Diff formats.
-   **Production-Grade Evals**:
    -   **Manage end-states**, not processes.
    -   Use **LLM-as-a-Judge** with human-in-the-loop auditing.
    -   Iterate quickly with a small set of real-world queries (~20) before scaling test sets.

## IV. Reference Resources

For deep-dives into specific topics, refer to the following:
-   [Building Effective Agents](references/building-effective-agents.md): Detailed architectural patterns and ACI guidelines.
-   [Building Agents with Skills](references/building-agents-with-skills.md): Skill library design and the 4-layer agent architecture.
-   [Multi-Agent Research Systems](references/multi-agent-research-system.md): Production challenges, error recovery, and evaluation frameworks.
-   [Long-Running Agent Architecture Guide](references/long-running-agent-architecture-guide.md): Practical harness patterns for long-lived agents, including session handoffs, externalized state, and end-to-end verification.
