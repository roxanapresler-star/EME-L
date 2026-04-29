# EME-L Documentation Project

## Project Overview

This repository contains documentation for EME-L programming language and EME.WMS warehouse management system, optimized for Context7 RAG search.

## Key Information

- **Language**: Russian for descriptions, English for code
- **Target**: Context7 (RAG-optimized documentation)
- **Source**: `vol_0.md` — consolidated source (12936 lines), parsed into 44 markdown files in `docs/`

## Documentation Structure

### EME-L Language — Basics (5)
- `docs/EME_L_Syntax.md` — Variables, 9 data types, operators, control flow
- `docs/EME_L_Classes.md` — Object model, Object(), IClass, static linking
- `docs/EME_L_Events.md` — All event handlers (24 dialog + controls + tables + browsers + integrator + reports)
- `docs/EME_L_Constants.md` — 13 groups of constants (DOCUMENT_TYPE_*, os*, MO_TYPE_*, ZONE_*)
- `docs/EME_L_IS_Functions.md` — is-functions (Bit, Browser, Context)

### EME-L Language — Data & Memory (5)
- `docs/EME_L_Database.md` — Query, BitBuffer, Array, MultiArray, Map
- `docs/EME_L_Database_Advanced.md` — DBWatch, MustBe* filters, transactions, large data
- `docs/EME_L_Memory.md` — Memory rules, Const, leaks, performance
- `docs/EME_L_Records_and_Fields.md` — Records, fields, browsers, chains, traversal modes
- `docs/EME_SQL_Advanced.md` — Full EME-SQL (SELECT, WITH, JOIN, UNION, TOTAL, NEW{})

### EME-L Language — Advanced (5)
- `docs/EME_L_Advanced_Features.md` — Components, ByRef/ByVal/RefOf/ValOf, tr() localization
- `docs/EME_L_Advanced_API_and_Patterns.md` — String (40+ methods), PropertyTree, Parameters, bots
- `docs/EME_L_HTTP_Integration.md` — HTTP architecture, TSD communication, Batch processing
- `docs/EME_L_Query_Reports.md` — Reports, print forms, bands, editable reports
- `docs/EME_L_Misc_and_Localization.md` — UI testing, robot testers, localization

### Business & Architecture (2)
- `docs/EME_WMS_Theory_and_Architecture.md` — WMS architecture, transactions, alignment
- `docs/Business_Analytics.md` — Platform history, base version components

### Administration (2)
- `docs/Server_and_Services_Setup.md` — Server, ServerJob, Task Monitor
- `docs/Performance_and_Logging.md` — Logging, diagnostics, profiling

### Integration (3)
- `docs/ERP_1C_Integration.md` — 1C ERP integration via intermediate SQL database
- `docs/Web_Services_and_API.md` — JSON API, SOAP
- `docs/PostgreSQL_ODBC_Integration.md` — ExternalDB for PostgreSQL via ODBC

### Build & DevOps (4)
- `docs/Build_Metaproject.md`, `docs/Build_Android_TSD.md`
- `docs/Hash_DB_and_3PL.md`, `docs/Database_Replication.md`

### GUI (5)
- `docs/GUI_DB_Structure.md`, `docs/GUI_Dialog_Editor.md`
- `docs/GUI_Project_Deployment.md`, `docs/GUI_Data_Slices_and_Lessons.md`
- `docs/GUI_Activist_and_Configurator.md`

### TSD & Warehouse Operations (4)
- `docs/TSD_Operations_and_Settings.md`, `docs/TSD_WDC_Measurement.md`
- `docs/TSD_WDC_Cell_Routing.md`, `docs/AI_Programming_Assistant.md`

### Special Modules & Platform (4)
- `docs/GUI_Fingerprint_Registration.md`, `docs/Working_Time_Control.md`
- `docs/EME_L_Examples.md`, `docs/EME_WMS_Linux.md`

### Deployment (5)
- `docs/Release_Deployment.md`, `docs/Test_Environment_and_TSD_Setup.md`
- `docs/Installer_Creation.md`, `docs/Updates_Methodology.md`
- `docs/System_Archiving_and_Priorities.md`

## Guidelines

1. Do not add content that doesn't exist in source documentation
2. Maintain Russian descriptions for semantic search
3. Keep code examples in English (EME-L syntax)
4. Update `llms.txt` and `context7.json` when making changes to docs

## Context7 Library

Library ID: `/eme-wms/eme-l`

Use Context7 tools when you need to search documentation:
- `context7_resolve-library-id` — Find library by name
- `context7_query-docs` — Query documentation

## Git

- Repository: https://github.com/eme-wms/EME-L
- Branch: main
