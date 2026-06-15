# AI Solution Notes

AI-assisted technical research notes, architecture briefs, and implementation specs.

This repository collects research reports for product, architecture, and engineering topics. Each report is designed to include both a human-readable web page and an AI/developer-oriented Markdown specification.

## Reports

| Report | Web Page | Spec |
|---|---|---|
| LINE OA Agentic Workflow | [Open report](./reports/line-oa-agentic-workflow/) | [spec.md](./reports/line-oa-agentic-workflow/spec.md) |

## Report Structure

Each report should live under `reports/<topic-slug>/`.

```text
reports/<topic-slug>/
├─ index.html
├─ spec.md
├─ README.md
└─ assets/
   └─ ...
```

## Suggested Workflow

1. Research or discuss a topic.
2. Produce a concise web page for human communication.
3. Produce a Markdown spec for AI coding agents or engineering follow-up.
4. Add the report to the root `index.html` and this `README.md`.
5. Publish with GitHub Pages.

