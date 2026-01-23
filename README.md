# Build in Public Report — 23/1/2026

## 🎯 PRODUCT: What Users Experience

**8 user-facing file(s) updated with focus on resilience and business logic**

No user-facing changes



### Risk Profile


**AI-Identified Risks**:
- Introduced Risk: Schema Staleness Leading to Application Mismatch: A persistent cache introduces the possibility that `data/schema-cache.json` might not reflect the most current upstream schema. If the application code evolves to rely on newer schema features or types not yet propagated to the cache, runtime errors or incorrect behavior could occur until the next successful synchronization.
- Introduced Risk: Cache Corruption or Inconsistency During Synchronization: Despite `[data-integrity]` flags, the `scripts/sync-schema-cache.ts` process itself is a potential point of failure. An interruption or error during the write operation to `data/schema-cache.json` (e.g., I/O error, script crash) could leave the cache file in a partially written, corrupted, or inconsistent state, making it unusable or leading to parsing errors by the application.
- Managed Risk: Reduced Upstream Service Load and Dependency: By caching schema information locally, the developer significantly mitigates the risk of overwhelming the upstream schema source with repeated requests and reduces the application's direct dependency on its continuous availability and performance, improving overall system stability.
- Managed Risk: Performance Bottleneck from Dynamic Schema Resolution: The caching mechanism directly addresses the performance risk associated with dynamically fetching or introspecting schema data at runtime or application startup, which can be a significant bottleneck for applications that frequently interact with data structures.

### Metrics
- Files Changed: 8
- Lines Added: 8
- Avg Complexity: 0.0/10

---

## 🔧 KNOW-HOW: Systems & Infrastructure

**12 infrastructure file(s) updated — systems orchestration, observability, and automation**

### Systems Built
**Schema Observability** (4x complexity weight)
- Purpose: Live database schema introspection and caching
- Files: data/schema-cache.json, scripts/sync-schema-cache.ts
- Value: Enables feature development without manual schema audits. Auto-syncs on git events.

**Forensic Analysis** (4x complexity weight)
- Purpose: AI-powered diff analysis extracting seniority signals from code changes
- Files: internal_pipeline/scripts/forensic_translate.ts
- Value: Converts raw diffs into architectural insights. Identifies risk patterns automatically.

**Report Generation** (5x complexity weight)
- Purpose: Two-bucket report engine (Product + Know-How separation)
- Files: internal_pipeline/scripts/generate_narratives.ts, internal_pipeline/scripts/generate_knowhow_narrative.ts, internal_pipeline/scripts/generate_product_narrative.ts
- Value: One `npm run combo` → Single authoritative report → Google Drive. Evidence-driven, no marketing.

**Diff Analysis Engine** (4x complexity weight)
- Purpose: Git diff parsing with architectural keyword detection and complexity scoring
- Files: internal_pipeline/scripts/analyze_diffs.ts
- Value: Flags data-integrity, security-critical, and architectural changes automatically.

**Workflow Orchestration** (5x complexity weight)
- Purpose: Git workflow automation triggering schema sync and reporting
- Files: scripts/merge-main.ts, scripts/combo.ts
- Value: Hidden in 1-2 lines each, but activates entire pipeline on: new branch, commit, merge to main.

### Architecture Overview
End-to-end code observability pipeline. Transforms git diffs into actionable narratives with architectural insights.

```
Git Event (branch/commit/merge)
    ↓
Workflow Orchestration (combo.ts)
    ↓
Schema Cache Sync (95 tables, 40 functions)
    ↓
Diff Analysis (complexity scoring, keyword detection)
    ↓
Forensic Translation (Gemini: risk patterns, seniority signals)
    ↓
Two-Bucket Narratives (Product + Know-How)
    ↓
Google Drive Publish (auto-uploaded, shareable, final)
```

### Integration Points
- Git workflow events (new branch, commits, merge to main)
- Supabase REST API (schema introspection)
- Google Generative AI (Gemini for forensic analysis)
- Google Drive API (report publishing)
- Pipeline orchestration (auto-triggered on git events)

### DX Improvements
✅ **Schema Awareness**: Developers now have live access to database schema without manual queries
✅ **Automated Risk Detection**: Architectural risks identified automatically, reducing code review friction
✅ **Report Generation**: `npm run combo` generates publishable 3-perspective report in 30 seconds
✅ **Workflow Automation**: Schema and reports auto-sync on git events (no manual trigger needed)

### Trade-Offs & Limitations
**Current Trade-Offs & Limitations**:
- **Schema Cache Limitations**: RLS policies and triggers require Supabase admin access (skipped in current setup)
- **Forensic Analysis**: Limited to 50 files per run (Gemini API cost); larger commits may need batching
- **Schema Evolution**: Cache captures current state only; migration history not tracked
- **Fallback Behavior**: If Gemini API fails, pipeline continues with complexity scoring alone (graceful degradation)

### Next Steps for Maintainability
- **Migration History Tracking**: Enhance schema cache to track schema evolution over time
- **Batch Forensic Analysis**: For commits >50 files, implement batching to reduce Gemini API costs
- **Custom Narrative Templates**: Allow per-audience templates (LinkedIn vs GitHub vs internal vs hiring)
- **Complexity Thresholds**: Set alert thresholds (e.g., email on high-complexity changes)
- **RLS Policy Capture**: If admin access available, include RLS policy analysis in reports

### Metrics
- Files Changed: 12
- Lines Added: 29
- Total Complexity: 20.0
- Avg Multiplier: 1.7x

---

## Summary

**Total Changes**: 20 files, +37 / -5 lines

**Bucket Distribution**:
- Product: 8 files, 0.0 complexity
- Know-How: 12 files, 20.0 complexity

---

_Evidence-driven report from git diffs. No marketing claims—just what was built and why it matters. Updated with each `npm run combo`._