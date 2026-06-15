# Multi-Agent Architecture Spec

This note defines when and how to evolve a single tool-calling agent into multiple collaborating agents.

## Core Position

Multi-agent is not the starting point. Use it only when single orchestrator + tools has clear failure modes.

```text
Start:
Single Orchestrator Agent + Tools

Evolve when needed:
Supervisor Agent
→ Knowledge Agent
→ Transaction Agent
→ Support Agent
```

## What Counts as Multi-Agent

Many tools do not equal many agents.

Multi-agent means there is more than one decision-making LLM runtime, each with its own:

- system prompt
- tool subset
- context contract
- model choice
- safety boundary

## When to Split

Split only when at least one of these is true:

- The tool list is too large and the model often picks the wrong tool.
- Different tasks need very different system prompts.
- Some tasks require stronger or cheaper models.
- Subtasks can run in parallel.
- High-risk capabilities need isolation.
- A specialist workflow has its own state machine.

## Common Topologies

### 1. Supervisor / Worker

```text
User
→ Supervisor Agent
  → Knowledge Agent
  → Transaction Agent
  → Support Agent
→ Final answer
```

Best when tasks are dynamic and the supervisor must decide who should work.

### 2. Agent as Tool

```text
Single Orchestrator
→ askKnowledgeSpecialist tool
→ askTransactionSpecialist tool
```

This is the easiest migration path from a single-agent architecture because a specialist agent can be wrapped behind the same tool contract.

### 3. Pipeline

```text
Planner → Executor → Reviewer
```

Best when the order is fixed.

### 4. Handoff

```text
Frontline Agent → Billing Agent → Technical Agent
```

Best when only one agent should own the conversation at a time.

## Main Risks

| Risk | Why it matters | Mitigation |
|---|---|---|
| Context loss | Agents do not automatically share the full conversation | Use structured task specs |
| Cost | Every agent may call an LLM | Keep depth shallow |
| Latency | Agent chains slow down UX | Parallelize only when useful |
| Inconsistency | Agents may disagree | Supervisor owns final answer |
| Debugging | Traces become trees | Log parent-child agent calls |
| Security | More agents means more permissions | Scope tools per agent |

## Recommended Migration Path

1. Build single orchestrator + tools.
2. Keep tool contracts strict.
3. When one tool category grows complex, wrap it as `agent-as-tool`.
4. If there are three or more specialist agents, add supervisor-worker orchestration.
5. Add trace tree, audit log, cost tracking, and depth limits.

## Relation to LINE OA

For LINE OA customer workflows:

- MVP should be single orchestrator + tools.
- Multi-agent is a future architecture, not the first milestone.
- Split only if Knowledge, Transaction, Booking, and Support become independently complex.

