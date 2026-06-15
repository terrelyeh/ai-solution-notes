# Single Agent Tool Calling Spec

This note defines a public, reusable architecture pattern for building a single-agent system that can choose between RAG retrieval and controlled tools.

## Core Position

Start with a single orchestrator agent plus tools. Do not begin with multiple independent agents unless the system has clear complexity, safety, or specialization pressure.

```text
User message
→ Single Orchestrator Agent
  → searchKnowledgeBase tool
  → liveDataLookup tool
  → transaction tool
  → createTicket tool
→ Final response
```

## Why Pure RAG Is Not Enough

Pure RAG works when the answer is in static documents. It does not work well for:

- real-time status
- user-specific account data
- inventory or booking availability
- actions such as cart, booking, ticket creation
- workflows that need confirmation

The shift is:

```text
Old:
Every question → retrieve documents → answer

New:
Question → model decides which tool to use → answer or act
```

## Tool Calling

Tools are backend-defined capabilities exposed to the model through schema.

The model requests a tool call. The backend executes it. The model never directly touches databases, APIs, credentials, or payment operations.

Example tool contract:

```ts
type Tool<TInput, TOutput> = {
  name: string
  description: string
  inputSchema: object
  requiresConfirmation?: boolean
  execute: (input: TInput, context: ToolContext) => Promise<TOutput>
}
```

## Agent Loop

```text
1. Send messages + tool definitions to model.
2. Model either answers or requests tool calls.
3. Backend validates and executes tool calls.
4. Tool results are sent back to the model.
5. Repeat until final answer or max step limit.
```

Hard requirements:

- max step limit
- tool allowlist
- input validation
- audit logging
- timeout handling
- fallback / human handoff

## RAG as a Tool

RAG should be one tool, not the whole application flow.

```ts
searchKnowledgeBase({
  query,
  filters: {
    department,
    category,
    visibility
  }
})
```

This lets the system handle hybrid requests:

```text
"What is the warranty policy, and can I start a return?"
→ searchKnowledgeBase(policy)
→ createHumanTicket or startReturn workflow
```

## Recommended Tool Categories

| Tool | Purpose |
|---|---|
| `searchKnowledgeBase` | SOP, FAQ, policy, product documentation |
| `lookupCustomer` | customer profile and conversation context |
| `searchProducts` | product, SKU, inventory, price |
| `addToCart` | add confirmed item to cart |
| `checkAvailability` | booking / ticket availability |
| `createBooking` | confirmed booking |
| `createHumanTicket` | human handoff |
| `writeAuditEvent` | audit trail |

## Security

- Backend owns all credentials.
- Tools are allowlisted per workspace / tenant.
- Tool inputs are untrusted and must be validated.
- Write actions require confirmation.
- Tool results are data, not instructions.
- Every tool call must write audit events.

## Provider Abstraction

Different LLM providers expose tool calling differently. Normalize them behind one internal interface:

```ts
type NormalizedModelTurn = {
  text?: string
  toolCalls?: Array<{
    id: string
    name: string
    input: unknown
  }>
  finishReason: "stop" | "tool_use" | "error"
}
```

## Implementation Roadmap

1. Build basic webhook / chat endpoint.
2. Add message log and workflow state.
3. Add `searchKnowledgeBase` tool.
4. Add one action tool.
5. Add confirmation gate.
6. Add human handoff tool.
7. Add audit log.
8. Add provider abstraction.
9. Add more tools.
10. Split into multi-agent only when there is a concrete need.

