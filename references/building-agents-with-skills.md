# Building Agents with Skills: Equipping Agents for Specialized Work

This article marks a major evolution in Anthropic's approach to agentic architecture. The core theme is **no longer building vertical agents for specific domains, but rather injecting "Skills" into generalized agents**, transforming high-IQ generalist talent into domain experts capable of solving real-world business problems.

## I. Structural Overview of Core Viewpoints

### 1. Paradigm Shift: From "Vertical Agents" to "General Agents + Code Interface"

-   Historically, the industry tended toward developing specialized agents for different domains (programming, finance, marketing).
-   As model capabilities improve, the industry has found that **"code" is the ultimate interface** for an agent to handle all digital work. General-purpose agents like Claude Code can complete various complex tasks like financial reporting by writing Python scripts, calling APIs, and operating file systems.

### 2. Core Pain Points & Solutions: What are "Skills"?

-   **Pain Point**: Current agents are like "brilliant math geniuses"—capable of reasoning through new problems from first principles but lacking the years of accumulated industry experience of an "experienced tax expert."
-   **Solution**: "Skills" are domain expertise, workflows, and best practices packaged into files (like Markdown or scripts). They can be version-controlled (Git), stored, and shared, directly empowering general-purpose agents.

### 3. Core Design: Progressive Disclosure

To prevent a sea of knowledge from overwhelming an agent's context window, Skills employ a three-level loading mechanism:

-   **Level 1 (Awareness)**: At runtime, only metadata (name and description, ~50 tokens) is shown to the model.
-   **Level 2 (Understanding)**: If the agent determines a skill is needed, it reads the full `SKILL.md` file (~500 tokens).
-   **Level 3 (Deep Dive)**: If more detail is required, it loads lower-level reference documents (2,000+ tokens) as needed.

### 4. New 4-Layer Agent Architecture

The system architecture is evolving into a combination of four clearly defined parts:

1.  **Agent Loop**: The core reasoning system that decides what to do next.
2.  **Agent Runtime**: The execution environment for code and file systems.
3.  **MCP Servers (Model Context Protocol)**: Responsible for connecting to external tools and data sources.
4.  **Skills Library**: Providing domain expertise and procedural guidance.

## II. High-Value Insights Based on the Article

### Insight 1: "File-based" Knowledge Completely Democratizes Agent Engineering

In traditional agent frameworks, adding capabilities often requires engineers to write system code or adjust complex JSON function declarations. Anthropic has reduced "Skills" to the **most common primal carrier: files**. This means building agents is no longer the exclusive privilege of engineers—product managers, financial analysts, and clinicians can write their own SOPs in simple language (Markdown) and directly transform them into agent capabilities. This low barrier will greatly stimulate the conversion of "implicit knowledge" within enterprises into "AI assets."

### Insight 2: "MCP + Skills" Constitute a New Operating System-level Division of Labor for AI

If the Agent is viewed as the "brain" in the AI era, Anthropic has clarified its infrastructure division:

-   **MCP (Model Context Protocol) is the "Hardware Driver"**: Responsible for handling external I/O (like reading company databases or searching the web).
-   **Skills are the "Application Software (Apps)"**: Responsible for telling the brain "what to do" in a specific scenario. For example, during competitive analysis, MCP pulls web and internal Notion data, while "Skills" guide the agent on how to integrate this data and what financial models to apply. This decoupling of "capability connection" and "knowledge guidance" keeps the architecture clean and extremely easy to scale.

### Insight 3: The Ultimate Solution to "Tool Overload" is "Code as Tools"

Traditional API tools have serious flaws: documentation isn't clear enough, models can't modify them, and too many tools bloat the context. The article reveals a stunning practice: **letting Agents write and save scripts as their own tools.** For example, if an agent frequently needs to apply company templates to slides, it can write a script as a tool and save it. The script code is inherently self-explanatory, called when needed, and can even be modified by the model itself based on new situations. This "self-empowerment" is incomparable to traditional hard-coded systems.

### Insight 4: Abandon "Giant Prompts"; "Progressive Context" is the Enterprise Best Practice

Many enterprises often stuff all regulations and background information into the System Prompt, resulting in high conversation costs and scattered attention. The "Skill" progressive disclosure mechanism (Metadata -> Main File -> Reference Materials) provides a standard paradigm. **Future enterprise AI systems should not load all knowledge at the beginning, but allow autonomous Agents to look at the "Table of Contents" first and precisely pull the "Body Text" like looking up a dictionary.** This is the optimal solution for balancing "professional breadth" and "reasoning efficiency."
