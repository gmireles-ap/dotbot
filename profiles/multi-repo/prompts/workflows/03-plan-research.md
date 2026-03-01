---
name: Plan Research
description: Create the 3 foundational research tasks via task_create_bulk
version: 1.0
---

# Plan Research

This workflow creates the initial research tasks that form the foundation of the multi-repo initiative lifecycle. These tasks use the research prompts as methodologies and produce structured output to the briefing directory.

## Prerequisites

Before running this workflow:
- Phase 0 (kickstart) must be complete — `briefing/initiative.md` must exist
- Phase 0.5 (plan product) must be complete — `mission.md` and `roadmap-overview.md` must exist

## Your Task

Create exactly 3 research tasks using `task_create_bulk`. These tasks cover the three foundational research streams:

### Step 1: Read Initiative Context

```
read_files({ files: [
  { path: ".bot/workspace/product/briefing/initiative.md" },
  { path: ".bot/workspace/product/mission.md" }
]})
```

Extract the initiative name and Jira key for task naming.

### Step 2: Create Research Tasks

```
mcp__dotbot__task_create_bulk({
  tasks: [
    {
      "name": "Research Atlassian for {INITIATIVE_NAME}",
      "description": "Conduct a comprehensive scan of all Jira tickets, Confluence pages, comments, and related documentation for {JIRA_KEY}. Produce a structured current status report covering scope, risks, blockers, similar projects, and recommendations.\n\nOutput: .bot/workspace/product/briefing/00_CURRENT_STATUS.md",
      "category": "research",
      "effort": "L",
      "priority": 1,
      "dependencies": [],
      "research_prompt": "atlassian.md",
      "acceptance_criteria": [
        "00_CURRENT_STATUS.md written to .bot/workspace/product/briefing/",
        "All Jira tickets related to the initiative catalogued",
        "Confluence documentation gaps identified",
        "Cross-source contradictions flagged",
        "Similar/predecessor projects analysed",
        "Recommended next actions provided"
      ],
      "steps": [
        "Read initiative.md for Jira key, initiative name, and context",
        "Load research methodology from prompts/research/atlassian.md",
        "Scan Jira: parent epic, linked issues, comments, status history",
        "Scan Confluence: pages referencing Jira key or initiative name",
        "Cross-reference Jira status vs Confluence documentation",
        "Identify similar/predecessor projects and extract lessons",
        "Write structured report to briefing/00_CURRENT_STATUS.md"
      ],
      "applicable_standards": [".bot/prompts/standards/global/research-output.md"],
      "applicable_agents": [".bot/prompts/agents/researcher/AGENT.md"]
    },
    {
      "name": "Research public and regulatory context for {INITIATIVE_NAME}",
      "description": "Conduct structured internet research to identify industry best practices, regulatory requirements, comparable case studies, technical patterns, and risk landscape for {INITIATIVE_NAME}.\n\nOutput: .bot/workspace/product/briefing/01_INTERNET_RESEARCH.md",
      "category": "research",
      "effort": "L",
      "priority": 2,
      "dependencies": [],
      "research_prompt": "public.md",
      "acceptance_criteria": [
        "01_INTERNET_RESEARCH.md written to .bot/workspace/product/briefing/",
        "Industry context and landscape documented",
        "Comparable case studies identified",
        "Regulatory and compliance requirements researched",
        "Technical patterns and best practices catalogued",
        "All sources cited with URLs"
      ],
      "steps": [
        "Read initiative.md for initiative name and business objective",
        "Load research methodology from prompts/research/public.md",
        "Research industry context and market landscape",
        "Identify comparable initiatives and case studies",
        "Research regulatory and compliance requirements",
        "Identify technical patterns and best practices",
        "Write structured report to briefing/01_INTERNET_RESEARCH.md"
      ],
      "applicable_standards": [".bot/prompts/standards/global/research-output.md"],
      "applicable_agents": [".bot/prompts/agents/researcher/AGENT.md"]
    },
    {
      "name": "Scan repos for impact of {INITIATIVE_NAME}",
      "description": "Identify all repositories affected by {JIRA_KEY} using source code search. Classify repos by tier (1-6) and impact (HIGH/MEDIUM/LOW). Map cross-repo dependencies. Produce a structured impact assessment.\n\nOutput: .bot/workspace/product/briefing/02_REPOS_AFFECTED.md\n\nDepends on: Atlassian and public research (for context and search terms).",
      "category": "research",
      "effort": "XL",
      "priority": 3,
      "dependencies": [
        "Research Atlassian for {INITIATIVE_NAME}",
        "Research public and regulatory context for {INITIATIVE_NAME}"
      ],
      "research_prompt": "repos.md",
      "acceptance_criteria": [
        "02_REPOS_AFFECTED.md written to .bot/workspace/product/briefing/",
        "All affected repos identified and classified by tier",
        "Impact levels assigned (HIGH/MEDIUM/LOW) per repo",
        "Cross-repo dependencies mapped",
        "Reference implementation pattern identified",
        "Deep dive sections for HIGH-impact repos included",
        "End-to-end data flow diagram provided"
      ],
      "steps": [
        "Read initiative.md for context and search terms",
        "Read 00_CURRENT_STATUS.md and 01_INTERNET_RESEARCH.md for domain context",
        "Load research methodology from prompts/research/repos.md",
        "Establish search terms from initiative domain",
        "Scan repos using Sourcebot/code search",
        "Classify repos by tier (1-6) and impact level",
        "Map cross-repo dependencies and integration points",
        "Identify reference implementation",
        "Write structured report to briefing/02_REPOS_AFFECTED.md"
      ],
      "applicable_standards": [".bot/prompts/standards/global/research-output.md"],
      "applicable_agents": [".bot/prompts/agents/researcher/AGENT.md"]
    }
  ]
})
```

### Step 3: Verify Creation

After `task_create_bulk` returns, verify:
1. All 3 tasks created successfully (check `created_count == 3`)
2. Tasks 1 and 2 have no dependencies
3. Task 3 depends on tasks 1 and 2
4. All tasks have `category: "research"` and `research_prompt` fields

Report the result to the user.

## Output

Three research tasks in `.bot/workspace/tasks/todo/`:
1. Atlassian research (no dependencies, priority 1)
2. Public/regulatory research (no dependencies, priority 2)
3. Repo impact scan (depends on 1 + 2, priority 3)

## Critical Rules

- Create exactly 3 tasks — no more, no fewer
- Use `task_create_bulk` — not individual `task_create` calls
- Include `research_prompt` field on each task
- Set correct dependencies: task 3 depends on tasks 1 and 2
- Use the initiative name and Jira key from `initiative.md` in task names
- Do NOT execute the research — only create the tasks
