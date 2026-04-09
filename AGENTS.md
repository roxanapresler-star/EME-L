# EME-L Documentation Project

## Project Overview

This repository contains documentation for EME-L programming language and EME.WMS warehouse management system, optimized for Context7 RAG search.

## Key Information

- **Language**: Russian for descriptions, English for code
- **Target**: Context7 (RAG-optimized documentation)
- **Source**: `wiki_origin/` directory contains original HTML documentation

## Documentation Structure

- `docs/README.md` — Main documentation index
- `docs/EMEL_Language_Basics.md` — EME-L syntax reference
- `docs/EME_WMS_Administration.md` — EME.WMS administration guide
- `docs/EMEL_Working_with_Records.md` — Working with records
- `docs/EMEL_SQL_Queries.md` — SQL queries in EME-L
- `docs/EMEL_Dialogs_Reports.md` — Dialogs and reports
- `docs/EMEL_EventHandlers.md` — Event handlers
- `docs/EMEL_Classes_Utility.md` — Utility classes (Query, Map, Array, etc.)
- `docs/EMEL_Constants_isFunctions.md` — Constants and is-functions
- `docs/EMEL_Practice_Tasks.md` — Practical tasks
- `docs/EME_WMS_Architecture.md` — System architecture
- `docs/EME_WMS_Priorities_Algorithms.md` — Priorities and algorithms

## Guidelines

1. Do not add content that doesn't exist in source documentation
2. Maintain Russian descriptions for semantic search
3. Keep code examples in English (EME-L syntax)
4. Update version in `docs/README.md` when making changes

## Context7 Library

Library ID: `/eme-wms/eme-l`

Use Context7 tools when you need to search documentation:
- `context7_resolve-library-id` — Find library by name
- `context7_query-docs` — Query documentation

## Git

- Repository: https://github.com/eme-wms/EME-L
- Branch: main