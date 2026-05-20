# Antigravity CLI — Capabilities Reference

Comprehensive reference of Antigravity's tools and capabilities that Claude Code
does NOT have natively. Use this to decide what to delegate.

## Exclusive Tools

### Google Web Search
Real-time internet search powered by Google's search index.

**Claude Code has:** No web search capability
**Antigravity has:** `google_web_search` with current, grounded results

**Delegate when:**
- Need current information (CVEs, latest versions, recent releases)
- Need to verify facts against live sources
- Need community opinions, benchmarks, or comparisons
- Need to check if a library/API is still maintained

---

### Image Generation
AI-powered image generation for mockups, assets, and diagrams.

**Claude Code has:** No image generation
**Antigravity has:** `generate_image` tool

**Delegate when:**
- Need UI mockups or wireframes
- Need visual assets (icons, illustrations, backgrounds)
- Need before/after comparison images
- Need diagrams that go beyond text/mermaid

---

### Codebase Investigator
Deep architectural analysis tool that maps entire codebases.

**Claude Code has:** Grep, Glob, Read (manual, file-by-file)
**Antigravity has:** `codebase_investigator` (automatic, holistic analysis)

**Delegate when:**
- Analyzing unfamiliar codebases for the first time
- Need a comprehensive architecture map
- Need to understand cross-file dependencies
- Need to identify design patterns in use

---

### Chrome DevTools
Browser automation, debugging, and performance analysis.

**Claude Code has:** No browser integration
**Antigravity has:** Full Chrome DevTools Protocol access

**Delegate when:**
- Need to debug a web page visually
- Need performance profiling (LCP, CLS, FID)
- Need to test accessibility
- Need to automate browser interactions
- Need to inspect network requests

---

### Science Database Skills (40+)

Specialized skills for querying biological and chemical databases.
Claude Code has NONE of these natively.

| Skill | Database | Use For |
|-------|----------|---------|
| `gnomad-database` | gnomAD | Allele frequencies, variant rarity, gene constraint |
| `uniprot-database` | UniProt | Protein metadata, function, taxonomy |
| `pubmed-database` | PubMed | Scientific literature search |
| `pdb-database` | PDB | 3D protein structures |
| `chembl-database` | ChEMBL | Bioactive molecules, drug targets |
| `clinvar-database` | ClinVar | Pathogenicity classifications |
| `ensembl-database` | Ensembl | Gene/transcript/protein IDs, VEP |
| `alphafold-database` | AlphaFold | Predicted protein structures |
| `opentargets-database` | Open Targets | Drug target discovery |
| `pubchem-database` | PubChem | Chemical compound data |
| `string-database` | STRING | Protein-protein interactions |
| `reactome-database` | Reactome | Pathway analysis |
| `clinical-trials-database` | ClinicalTrials.gov | Clinical trial data |
| `interpro-database` | InterPro | Protein domains and families |
| `gtex-database` | GTEx | Gene expression by tissue |
| `dbsnp-database` | dbSNP | SNP/variant lookup |
| `quickgo-database` | QuickGO | Gene Ontology annotations |
| `jaspar-database` | JASPAR | TF binding profiles |
| `pymol` | PyMOL | Protein structure visualization |
| ... and 20+ more | | |

---

### Persistent Memory
Cross-session knowledge retention.

**Claude Code has:** CLAUDE.md (manual), `/memory` command
**Antigravity has:** `save_memory` tool (automatic, searchable)

---

## Shared Tools (Both Have)

Both Claude Code and Antigravity have these tools. Generally, do these yourself
unless the task is very large or requires many sequential steps.

| Tool | Notes |
|------|-------|
| File Read/Write | Both can read and write files |
| Terminal/Bash | Both can run shell commands |
| Search (grep/glob) | Both can search codebases |
| Code Generation | Both can generate code |
| Code Review | Both can review code |

## Capability Matrix

| Capability | Claude Code | Antigravity | Who Should Do It? |
|-----------|:-----------:|:-----------:|-------------------|
| Simple code edits | ✅ | ✅ | Claude Code (faster) |
| Multi-file refactors | ✅ | ✅ | Antigravity (preserves your context) |
| Web search | ❌ | ✅ | **Antigravity (exclusive)** |
| Image generation | ❌ | ✅ | **Antigravity (exclusive)** |
| Codebase architecture | 🟡 (manual) | ✅ (automatic) | Antigravity (better tool) |
| Browser debugging | ❌ | ✅ | **Antigravity (exclusive)** |
| Science queries | ❌ | ✅ | **Antigravity (exclusive)** |
| Interactive conversation | ✅ | 🟡 (limited in -p mode) | Claude Code (better UX) |
| Token-heavy analysis | ✅ (expensive) | ✅ | Antigravity (saves your tokens) |
| Quick one-liners | ✅ | ✅ | Claude Code (no overhead) |
