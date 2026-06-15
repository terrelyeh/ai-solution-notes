# LINE OA Agentic Workflow

This report explains how to build a LINE Official Account AI assistant that can answer questions from internal documents, execute controlled business workflows, hand off to human support, and preserve audit logs.

## Files

- [index.html](./index.html): human-readable architecture explainer
- [spec.md](./spec.md): AI/developer-oriented implementation spec
- [assets/hero.png](./assets/hero.png): report hero image

## Key Topics

- LINE OA as customer entry point
- Agentic workflow architecture
- MVP stance: single orchestrator agent + tools, multi-agent-ready
- Supervisor / Knowledge / Transaction agent roles
- RAG document retrieval
- Confirmation gate
- Human handoff
- Ticket / case management
- Audit logs
- User experience and response-time optimization

## Related Background

- [Single Agent Architecture](https://engenie-eg.vercel.app/docs/agent-architecture.html): tool calling, agent loop, and RAG as one tool.
- [Multi-Agent Architecture](https://engenie-eg.vercel.app/docs/multi-agent-architecture.html): when to split into multiple agents and how to structure them.

