# Research Agent (Deep Agents)

A small research agent built with LangChain's [`deepagents`](https://reference.langchain.com/javascript/deepagents). Give it a topic; it plans, delegates web research to a sub-agent, saves notes, writes a cited `report.md`, and has a second sub-agent critique it.

## Why this is a "deep" agent

| Deep-agent part | How it is used here |
|---|---|
| Planning tool | The lead agent calls `write_todos` to break the topic into 3-4 sub-questions |
| Sub-agents | `researcher` (has web search) and `critic` (review only), each with its own isolated context |
| File system | Notes go to `/notes/*.md` and the result to `/report.md`, so long work does not fill the context window |
| Detailed prompt | `src/prompts.ts` defines the workflow, limits and citation rules |

## Run

```bash
npm install
cp .env.example .env     # add ANTHROPIC_API_KEY and TAVILY_API_KEY
npm start -- "How do vector databases speed up similarity search?"
```

Output files are written to `./output` (`report.md`, `notes/`).

## Design decisions

- **Separate researcher and critic:** the critic never searches, so it can only judge what is in the report. This gives a cheap review step.
- **Bounded work:** at most 4 research tasks, 3 searches each, plus a recursion limit, to keep cost and runtime predictable.
- **Least privilege:** only the search tool is exposed. The agent's files live in memory and are written only inside `./output` (path traversal is blocked), because deep agents trust the model and boundaries must be enforced by the tools.
- **Citations from tools only:** the prompt forbids invented sources, and the critic checks claims against sources.

## Known limits

- It can still produce wrong or weakly supported claims. Read the report and open the cited URLs.
- Web search quality limits report quality.
- No automated tests yet; a next step is a small evaluation set (topic, expected facts) and checks that every cited URL appeared in a search result.
- The `deepagents` API changes quickly. If a type error appears on `subagents` fields, check the current docs.
# ResearchAgent
