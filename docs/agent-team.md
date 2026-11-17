# Agent team

I will use this custom agent team to build Mona's Project Pulse dashboard, orchestrated with GitHub Copilot CLI in a Codespace:

| Agent | Target model | Responsibility | Definition |
| --- | --- | --- | --- |
| Orchestrator | Claude Opus 5.5 (copilot) | Coordinates the project: gets the plan, assigns specialist tasks and file scopes, manages sequencing, integrates and verifies the result, and reports progress. | `.github/agents/orchestrator.agent.md` |
| Planner | Claude Opus 5.5 (copilot) | Researches the repository and relevant documentation, then creates an implementation plan with steps, file assignments, dependencies, risks, edge cases, and validation expectations. | `.github/agents/planner.agent.md` |
| Designer | Gemini 3.7 Flash (copilot) | Shapes the dashboard's user experience and visual design, including accessibility, information hierarchy, interaction flow, responsive layout, and Project Pulse styling. | `.github/agents/designer.agent.md` |
| Coder | GPT-5.5 (copilot) | Implements the assigned application logic and runnable-app support, following repository patterns and validating the changes. | `.github/agents/coder.agent.md` |
