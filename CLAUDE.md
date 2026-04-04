# Vela Documentation — CLAUDE.md

## Project overview

This is the Mintlify documentation site for Vela, an event ingestion and notification platform. The docs cover the TypeScript SDK, Python SDK, CLI tool, API reference, and UI guides.

## Tech stack

- **Framework**: Mintlify (MDX-based documentation)
- **Config**: `docs.json` — navigation, theme, branding, OpenAPI reference
- **API Reference**: Auto-generated from `api-reference/openapi.json` (OpenAPI 3.1.0) via MDX stubs with `openapi` frontmatter
- **Styling**: Vela brand colors — iris `#a78bfa` (primary), obsidian `#18181b` (dark), linen `#fafaf9` (light)

## Directory structure

```
docs/
├── docs.json                      # Mintlify config (nav, theme, openapi)
├── index.mdx                      # Landing page
├── quickstart.mdx                 # Getting started guide
├── concepts.mdx                   # Core concepts
├── ui-walkthrough.mdx             # Dashboard UI walkthrough with screenshots
├── credentials/                   # Auth credential guides
├── sdks/
│   ├── typescript/                # TypeScript SDK docs (11 pages)
│   └── python/                    # Python SDK docs (10 pages)
├── cli/                           # CLI tool docs (7 pages)
├── api-reference/
│   ├── openapi.json               # OpenAPI 3.1.0 spec (source of truth)
│   ├── introduction.mdx           # API overview (manual)
│   ├── authentication.mdx         # Auth guide (manual)
│   ├── apps/                      # MDX stubs → openapi frontmatter
│   ├── schemas/
│   ├── events/
│   └── notification-rules/
└── images/                        # Screenshots and SVG diagrams
```

## Key conventions

- All API reference endpoint pages are thin MDX stubs that use `openapi: "METHOD /path"` frontmatter to auto-generate from `openapi.json`. Do not write endpoint docs manually — update the OpenAPI spec instead.
- Use Mintlify components: `Frame` for images, `CodeGroup` for multi-language examples, `Accordion`/`AccordionGroup` for collapsible content, `Steps`/`Step` for sequential guides, `Note`/`Tip`/`Warning` for callouts.
- Images go in `images/` and are referenced as `/images/filename.png` in MDX.
- Navigation structure is defined in `docs.json` under `navigation.tabs`. Each tab has groups of pages.
- This repo is meant to be pushed to a separate docs repository for Mintlify deployment (not part of the main vela-be repo).

## Common tasks

- **Add a new guide page**: Create an MDX file, add it to the appropriate group in `docs.json` navigation.
- **Add a new API endpoint**: Add the path/operation to `api-reference/openapi.json`, create a stub MDX file with `openapi` frontmatter, add the page to `docs.json` navigation.
- **Update API endpoint docs**: Edit `api-reference/openapi.json` — the MDX stubs auto-render from it.
- **Add images**: Place in `images/`, reference as `/images/filename.png` inside `<Frame>` components.

## Do not

- Do not write manual API endpoint documentation in MDX — use the OpenAPI spec.
- Do not edit auto-generated content in API reference stub files beyond the frontmatter.
- Do not add pages without also adding them to `docs.json` navigation (they won't appear in the sidebar).
