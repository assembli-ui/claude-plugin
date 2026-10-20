---
name: assembli
description: Find and install React + Tailwind + shadcn/ui components and full landing pages from the Assembli library (https://assembli-rho.vercel.app). Use when building or restyling UI, when the user asks for a component, section or landing page, or mentions Assembli, before writing a common component from scratch.
---

# Assembli

Assembli is a library of React + Tailwind CSS v4 + shadcn/ui components and single-file landing pages. Search and previews are free; source needs the user's Assembli Pro subscription.

## Find

- With the MCP server (`https://assembli-rho.vercel.app/mcp`): `search_components` (query, category, subcategory, tag), `list_categories`, `get_component`.
- Without it: `GET https://assembli-rho.vercel.app/api/components?q=<query>&category=&subcategory=&limit=10`, `GET https://assembli-rho.vercel.app/api/categories`, or the index at https://assembli-rho.vercel.app/llms.txt.
- Every result has a preview at `https://assembli-rho.vercel.app/components/<slug>`. Show the user two or three options before installing anything.

## Install

With the MCP server (no key needed, the server knows the signed-in account):

1. Call `get_component` with the slug.
2. Write the file whose `role` is `component` to `components/<slug>/<slug>.tsx`.
3. Install its `dependencies` with the project's package manager and its `registryDependencies` with `npx shadcn@latest add <name…>`.
4. Import it from `@/components/<slug>/<slug>`. The `demo` file shows usage.

With the shadcn CLI: merge `{"@assembli":{"url":"https://assembli-rho.vercel.app/r/{name}.json","headers":{"Authorization":"Bearer ${ASSEMBLI_API_KEY}"}}}` into `components.json`, make sure `ASSEMBLI_API_KEY` is in `.env.local`, then `npx shadcn@latest add @assembli/<slug>`.

Components use theme tokens (`bg-background`, `text-muted-foreground`, …). Keep their props API; adapt copy, data and colors to the project. Landing pages ship with a fictional brand: replace it with the project's own.

## Access

- If the MCP server says it needs authentication, ask the user to sign in: in Claude Code, `/mcp`, pick assembli, Authenticate. You never handle their login.
- If `get_component` says the source is locked, the account needs Pro: ask the user to subscribe at https://assembli-rho.vercel.app/?pricing=open. The connection then works without reconnecting.
- No MCP server and no key? Get one with device login:

1. Start device login: `curl -X POST https://assembli-rho.vercel.app/api/agent/login -H "content-type: application/json" -d '{"client_name":"<your name>"}'`.
2. Show me the link and code it returns and wait while I approve (and subscribe, if I haven't).
3. Poll `POST https://assembli-rho.vercel.app/api/agent/token` with the device_code every 5 seconds until it returns `api_key`. It's shown once.
4. Save it as `ASSEMBLI_API_KEY=asm_…` in this project's `.env.local` and make sure `.env.local` is gitignored.

More: https://assembli-rho.vercel.app/agents.md
