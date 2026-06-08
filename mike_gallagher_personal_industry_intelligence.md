# Mike Gallagher Personal Industry Intelligence KB

**File:** `mike_gallagher_personal_industry_intelligence.md`  
**Purpose:** Design notes and implementation starting point for a personal industry intelligence knowledge base focused on the ETRM/CTRM industry, beginning with Endur / Findur / Openlink-related material.  
**Preferred ingestion model:** Option B — semi-automatic classification.

---

## 1. Objective

Build a personal industry knowledge base for ETRM/CTRM information, starting with a simple Endur-focused document vault.

The knowledge base should support future use by tools such as ChatGPT, Codex, or other AI-enabled workflows. The near-term goal is local organization and indexing. Remote access can be added later, but it is not a requirement for the initial build.

This is not intended to be a broad crawler across the entire computer. The system should work from a curated folder where documents are intentionally placed.

---

## 2. Core Principles

### 2.1 Keep the folder structure simple

The first version should avoid an overly detailed taxonomy. The ingestion process should do most of the classification through metadata and tags.

### 2.2 Use metadata, not folders, for most classification

Folders should answer the basic question: “What stage is this document in?”

Metadata should answer deeper questions:

- Who authored it?
- Is it public?
- Is it vendor-authored?
- Was it received from a customer or partner?
- Can it be used in default search?
- Can it be used in private-only search?
- Can it be used to generate external/client-facing content?
- What products and topics does it relate to?

### 2.3 Maintain source and usage boundaries

Some documents may be authored by Openlink / ION or received from customers, partners, or industry contacts. These may be acceptable to keep locally for personal reference, but they should be clearly identified and optionally excluded from default or external-output-safe searches.

### 2.4 Separate private reference from reusable output

The KB should distinguish between:

- Documents that can be used for private reference.
- Documents that can be used for general/default Q&A.
- Documents that can be used to generate external-facing materials such as articles, proposals, consulting collateral, or public summaries.

---

## 3. Initial Folder Structure

Recommended root folder:

```text
D:\Knowledge\Endur-KB\
```

Initial structure:

```text
D:\Knowledge\Endur-KB\
  00_Inbox\
  01_Source-Docs\
    Public\
    Vendor-Authored\
    Customer-Partner-Provided\
    Unknown-Needs-Review\
  02_My-Notes\
  03_Extracted\
  04_Index\
  05_Reports\
  99_Excluded\
```

### 3.1 Folder definitions

#### `00_Inbox`

Landing area for newly collected files.

Files placed here have not yet been classified or approved for indexing.

Examples:

- PDFs
- Word documents
- Markdown files
- Text files
- Web page exports
- Screenshots or images
- Product guides
- Help guides
- Personal notes

#### `01_Source-Docs`

Curated source documents after intake review.

Subfolders are intentionally simple:

```text
01_Source-Docs\
  Public\
  Vendor-Authored\
  Customer-Partner-Provided\
  Unknown-Needs-Review\
```

These folders provide a coarse grouping. Metadata provides the real classification.

#### `02_My-Notes`

User-authored notes, analysis, summaries, and working documents.

This should become the highest-value and safest part of the KB.

Examples:

```text
02_My-Notes\
  endur-overview.md
  endur-architecture-notes.md
  endur-vs-findur.md
  etrm-ctrm-glossary.md
  common-implementation-risks.md
```

#### `03_Extracted`

Generated text or markdown extracted from PDFs, Word documents, and other source formats.

This folder should be treated as generated output. Do not manually edit these files unless there is a deliberate correction process.

#### `04_Index`

Indexes and databases used by search and RAG tooling.

Possible contents:

```text
04_Index\
  metadata.sqlite
  vector_index\
  keyword_index\
  ingest_state.json
```

#### `05_Reports`

Generated reports from the ingestion process.

Examples:

```text
05_Reports\
  intake-review-2026-05-22.md
  unclassified-documents.md
  vendor-authored-documents.md
  external-output-safe-documents.md
```

#### `99_Excluded`

Documents intentionally excluded from indexing.

Use this for documents that are stale, duplicate, restricted, irrelevant, too sensitive, or not appropriate for AI search.

---

## 4. Document Metadata Strategy

Each source document should have an associated metadata sidecar file.

Preferred sidecar format: YAML.

Example:

```text
01_Source-Docs\Vendor-Authored\endur-product-guide.pdf
01_Source-Docs\Vendor-Authored\endur-product-guide.yml
```

The sidecar file should use the same base filename as the document.

---

## 5. Required Metadata Fields

Each document should have metadata fields like the following:

```yaml
title:
author:
publisher:
source_category:
origin:
confidentiality:
usage_policy:
default_search:
private_search:
external_output_allowed:
product:
topics:
date_received:
date_published:
document_version:
source_path:
extracted_text_path:
hash:
ingestion_status:
classification_confidence:
review_required:
review_notes:
notes:
```

---

## 6. Suggested Metadata Values

### 6.1 `source_category`

Allowed values:

```yaml
source_category:
  - my_note
  - public_web
  - public_vendor
  - vendor_authored
  - customer_partner_provided
  - analyst_research
  - conference_public
  - job_posting
  - unknown
```

Definitions:

| Value | Meaning |
|---|---|
| `my_note` | Written by Mike Gallagher |
| `public_web` | Publicly available web content |
| `public_vendor` | Public vendor material |
| `vendor_authored` | Vendor-authored but not clearly public |
| `customer_partner_provided` | Provided by customer, partner, or industry contact |
| `analyst_research` | Analyst or research material |
| `conference_public` | Publicly distributed conference content |
| `job_posting` | Job descriptions or market signals |
| `unknown` | Source is not yet clear |

### 6.2 `origin`

Allowed values:

```yaml
origin:
  - self_authored
  - downloaded_publicly
  - vendor_public_site
  - received_from_customer
  - received_from_partner
  - received_from_colleague
  - exported_from_web
  - unknown
```

### 6.3 `confidentiality`

Allowed values:

```yaml
confidentiality:
  - public
  - customer_distributed
  - vendor_distributed
  - personal_reference
  - unknown
  - restricted
```

### 6.4 `usage_policy`

Allowed values:

```yaml
usage_policy:
  - unrestricted_personal
  - public_reuse_allowed
  - personal_reference_only
  - do_not_use_for_external_output
  - do_not_index
```

### 6.5 Search flags

```yaml
default_search: include | exclude
private_search: include | exclude
external_output_allowed: true | false
```

---

## 7. Example Metadata Files

### 7.1 User-authored note

```yaml
title: Endur Architecture Notes
author: Mike Gallagher
publisher:
source_category: my_note
origin: self_authored
confidentiality: personal_reference
usage_policy: unrestricted_personal
default_search: include
private_search: include
external_output_allowed: true
product:
  - Endur
  - Findur
topics:
  - architecture
  - services
  - integration
date_received:
date_published: 2026-05-22
document_version: 1.0
source_path: D:\Knowledge\Endur-KB\02_My-Notes\endur-architecture-notes.md
extracted_text_path:
hash:
ingestion_status: approved
classification_confidence: high
review_required: false
review_notes:
notes: Personal industry notes created by Mike Gallagher.
```

### 7.2 Public vendor document

```yaml
title: Public Endur Product Overview
author: ION
publisher: ION
source_category: public_vendor
origin: vendor_public_site
confidentiality: public
usage_policy: public_reuse_allowed
default_search: include
private_search: include
external_output_allowed: true
product:
  - Endur
topics:
  - product overview
  - ETRM
date_received: 2026-05-22
date_published:
document_version:
source_path: D:\Knowledge\Endur-KB\01_Source-Docs\Public\public-endur-overview.pdf
extracted_text_path: D:\Knowledge\Endur-KB\03_Extracted\public-endur-overview.md
hash:
ingestion_status: approved
classification_confidence: medium
review_required: false
review_notes:
notes: Public vendor material.
```

### 7.3 Vendor-authored document received from a customer or partner

```yaml
title: Endur Product Guide
author: Openlink
publisher: Openlink / ION
source_category: vendor_authored
origin: received_from_customer
confidentiality: customer_distributed
usage_policy: personal_reference_only
default_search: exclude
private_search: include
external_output_allowed: false
product:
  - Endur
  - Findur
topics:
  - product guide
  - help guide
  - configuration
date_received: 2026-05-22
date_published:
document_version:
source_path: D:\Knowledge\Endur-KB\01_Source-Docs\Vendor-Authored\endur-product-guide.pdf
extracted_text_path: D:\Knowledge\Endur-KB\03_Extracted\endur-product-guide.md
hash:
ingestion_status: approved_private_only
classification_confidence: medium
review_required: false
review_notes:
notes: Vendor-authored guide retained for personal reference only. Excluded from default and external-output-safe searches.
```

### 7.4 Unknown-source document

```yaml
title: Unknown Endur Notes
author:
publisher:
source_category: unknown
origin: unknown
confidentiality: unknown
usage_policy: do_not_use_for_external_output
default_search: exclude
private_search: exclude
external_output_allowed: false
product:
  - Endur
topics: []
date_received: 2026-05-22
date_published:
document_version:
source_path: D:\Knowledge\Endur-KB\01_Source-Docs\Unknown-Needs-Review\unknown-endur-notes.pdf
extracted_text_path:
hash:
ingestion_status: needs_review
classification_confidence: low
review_required: true
review_notes: Source unclear. Do not index until reviewed.
notes:
```

---

## 8. Search Profiles

The system should support at least three search profiles.

### 8.1 Default Search

Used for normal Q&A.

Includes:

- `my_note`
- `public_web`
- `public_vendor`
- `conference_public`
- public job postings
- user-authored summaries

Excludes:

- vendor-authored non-public documents
- customer/partner-provided documents
- unknown-source documents
- documents marked `do_not_index`
- documents marked `default_search: exclude`

Rule:

```text
default_search == include
```

### 8.2 Private Reference Search

Used for private personal research only.

Includes:

- My notes
- Public documents
- Vendor-authored personal reference documents
- Customer/partner-provided personal reference documents

Excludes:

- `usage_policy: do_not_index`
- documents in `99_Excluded`
- documents marked `private_search: exclude`

Rule:

```text
private_search == include
AND usage_policy != do_not_index
```

### 8.3 External-Output-Safe Search

Used for drafting content that could leave the personal computer, such as:

- Articles
- LinkedIn posts
- Consulting material
- Proposals
- Client-facing summaries
- Public product comparisons

Includes only:

- User-authored notes
- Public documents
- Public vendor documents
- Public web material
- User-authored summaries that do not quote restricted material

Excludes:

- Vendor-authored non-public documents
- Customer/partner-provided documents
- Unknown-source documents
- Anything marked `external_output_allowed: false`

Rule:

```text
external_output_allowed == true
```

---

## 9. Ingestion Model: Option B — Semi-Automatic Classification

The ingestion process should classify documents automatically where possible but require human review before uncertain or sensitive documents are indexed.

### 9.1 High-level flow

```text
1. User drops files into 00_Inbox
2. Ingestion scans 00_Inbox
3. System computes file hash and detects file type
4. System extracts text and basic metadata
5. System performs automatic classification
6. System creates draft YAML sidecar
7. System suggests target folder
8. System assigns search policy
9. User reviews and approves classification
10. System moves/copies file into curated folder
11. System writes final YAML sidecar
12. System extracts text into 03_Extracted
13. System updates metadata database
14. System indexes approved documents
15. System generates an intake report
```

### 9.2 Classification should be conservative

If source or permissions are unclear, classify as:

```yaml
source_category: unknown
usage_policy: do_not_use_for_external_output
default_search: exclude
private_search: exclude
external_output_allowed: false
review_required: true
```

Unknown documents should not enter default search automatically.

### 9.3 Human review required when

Human review should be required if:

- Author cannot be determined.
- Publisher cannot be determined.
- The document appears vendor-authored but is not clearly public.
- The document appears customer/partner-provided.
- The document contains confidentiality markers.
- The document contains words like confidential, proprietary, internal, restricted, NDA, draft, client-specific, customer-specific.
- The document has no obvious source.
- The document is a duplicate with conflicting metadata.
- The classification confidence is low.
- The document is being considered for external-output-safe indexing.

---

## 10. Suggested Classification Signals

The ingestion process should inspect:

### 10.1 File metadata

- Filename
- Extension
- Created date
- Modified date
- PDF metadata
- Word document properties
- Author field
- Company field
- Title field
- Subject field
- Keywords field

### 10.2 Document text

Search for terms like:

```text
Openlink
OpenLink
ION
Endur
Findur
ETRM
CTRM
product guide
help guide
user guide
configuration guide
implementation guide
confidential
proprietary
internal use only
customer confidential
NDA
not for distribution
```

### 10.3 Source clues

Potential source indicators:

| Clue | Suggested classification |
|---|---|
| Author is Mike Gallagher | `my_note` |
| Publisher is ION and source URL is public | `public_vendor` |
| Author is Openlink / ION and no public source known | `vendor_authored` |
| File came from customer/partner folder | `customer_partner_provided` |
| Contains confidentiality marker | review required, default exclude |
| Source unknown | `unknown`, review required |

---

## 11. Suggested Ingestion Approval Workflow

For each new file, the system should present a review card like this:

```text
File: endur-product-guide.pdf
Detected title: Endur Product Guide
Detected author: Openlink
Detected publisher: Openlink / ION
Detected product: Endur
Detected topics: product guide, configuration, help
Detected sensitivity: vendor-authored, not clearly public

Suggested classification:
  source_category: vendor_authored
  origin: received_from_customer_or_partner
  confidentiality: customer_distributed
  usage_policy: personal_reference_only
  default_search: exclude
  private_search: include
  external_output_allowed: false

Suggested destination:
  01_Source-Docs\Vendor-Authored\

Approve / Edit / Exclude / Skip?
```

The review process can initially be a command-line prompt, markdown report, or simple local web page.

---

## 12. Indexing Rules

### 12.1 Do not index everything

Only index documents that are approved.

### 12.2 Keep index scope explicit

Indexing should support search filters:

```text
--profile default
--profile private
--profile external-safe
--author Openlink
--publisher ION
--source-category vendor_authored
--product Endur
--topic configuration
```

### 12.3 Recommended index types

Use both keyword and semantic search.

Recommended:

- Keyword search for exact terms, filenames, product names, phrases, and dates.
- Vector search for semantic similarity and conceptual questions.
- Metadata filters for author, publisher, source category, search profile, and product.

### 12.4 Why hybrid search matters

For an industry KB, exact terms matter.

Examples:

- Endur
- Findur
- Openlink
- OpenLink
- ION
- ETRM
- CTRM
- risk
- settlements
- confirmations
- nominations
- market data
- configuration

Pure semantic search can miss exact terminology. Pure keyword search can miss related concepts. Use both.

---

## 13. Suggested Technical Architecture

Initial build should be local and simple.

```text
D:\Knowledge\Endur-KB\
  source files
  metadata sidecars
  extracted text
  SQLite metadata database
  keyword search
  vector index
  CLI search tool
```

### 13.1 Recommended components

| Need | Suggested tool/library |
|---|---|
| Script language | Python |
| Metadata database | SQLite |
| YAML parsing | PyYAML |
| PDF text extraction | pypdf, pymupdf, or docling |
| Word extraction | python-docx |
| Markdown/text support | built-in Python file handling |
| File hashing | hashlib |
| Keyword search | SQLite FTS5 |
| Vector search | Chroma, LanceDB, or Qdrant |
| Embeddings | OpenAI embeddings or local embeddings |
| CLI | argparse or Typer |
| Reports | Markdown output |

### 13.2 Avoid overbuilding initially

The first implementation can be a CLI tool.

Suggested commands:

```powershell
kb ingest scan
kb ingest review
kb ingest approve
kb index rebuild
kb search "Endur configuration" --profile default
kb search "Endur configuration" --profile private
kb search "Endur configuration" --profile external-safe
kb list --source-category vendor_authored
kb report intake
```

---

## 14. Suggested Data Model

### 14.1 SQLite tables

#### `documents`

```sql
CREATE TABLE documents (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    title TEXT,
    author TEXT,
    publisher TEXT,
    source_category TEXT,
    origin TEXT,
    confidentiality TEXT,
    usage_policy TEXT,
    default_search TEXT,
    private_search TEXT,
    external_output_allowed INTEGER,
    product TEXT,
    topics TEXT,
    date_received TEXT,
    date_published TEXT,
    document_version TEXT,
    source_path TEXT NOT NULL,
    extracted_text_path TEXT,
    hash TEXT UNIQUE,
    ingestion_status TEXT,
    classification_confidence TEXT,
    review_required INTEGER,
    review_notes TEXT,
    notes TEXT,
    created_at TEXT,
    updated_at TEXT
);
```

#### `document_chunks`

```sql
CREATE TABLE document_chunks (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    document_id INTEGER NOT NULL,
    chunk_number INTEGER NOT NULL,
    chunk_text TEXT NOT NULL,
    source_path TEXT,
    page_number INTEGER,
    section_heading TEXT,
    created_at TEXT,
    FOREIGN KEY(document_id) REFERENCES documents(id)
);
```

#### `document_chunks_fts`

Use SQLite FTS5 for keyword search.

```sql
CREATE VIRTUAL TABLE document_chunks_fts
USING fts5(chunk_text, content='document_chunks', content_rowid='id');
```

---

## 15. Chunking Guidance

The system should split extracted text into chunks suitable for retrieval.

Suggested starting point:

```text
chunk_size: 800-1200 words
chunk_overlap: 100-150 words
```

Where possible, preserve:

- Page numbers
- Section headings
- Document title
- Filename
- Author
- Source category

Each retrieved answer should cite:

- Document title
- File path or filename
- Page number if available
- Section heading if available
- Search profile used

---

## 16. Answering Rules for Future AI Use

When an AI tool uses this KB, it should follow these rules:

1. Always state the search profile used.
2. Cite source documents.
3. Distinguish between sourced facts and user analysis.
4. Do not use private-only vendor/customer documents for external-facing content.
5. If no approved source is found, say so.
6. Prefer user-authored notes for external-safe synthesis.
7. Use vendor/customer-provided documents only for private reference unless explicitly allowed by metadata.
8. Never assume unknown-source material is safe to reuse.
9. Use metadata filters before answering.
10. When source conflicts exist, surface the conflict instead of smoothing it over.

---

## 17. External Output Safety Rules

When generating anything that may be shared externally, the system must use only documents where:

```text
external_output_allowed == true
```

The system should avoid direct quotes unless the source is public and citation/usage is appropriate.

For external content, prefer:

- Mike Gallagher-authored notes
- Public vendor materials
- Public web sources
- Public conference materials
- Public job postings
- User-authored summaries

Exclude:

- Vendor-authored non-public guides
- Customer/partner-provided files
- Unknown-source documents
- Any document with confidentiality markers
- Anything marked `do_not_use_for_external_output`

---

## 18. First Codex Task List

When ready to build, provide Codex this markdown and ask it to create a small local Python project.

### 18.1 Initial project goal

Build a local CLI-based Endur KB ingestion and search prototype.

### 18.2 Minimum viable deliverables

Codex should create:

```text
endur_kb/
  README.md
  requirements.txt
  kb/
    __init__.py
    cli.py
    config.py
    ingest.py
    classify.py
    extract.py
    metadata.py
    database.py
    search.py
    reports.py
  tests/
    test_metadata.py
    test_classify.py
```

### 18.3 Required CLI commands

```powershell
python -m kb.cli init --root "D:\Knowledge\Endur-KB"
python -m kb.cli scan
python -m kb.cli classify
python -m kb.cli review-report
python -m kb.cli approve --file "<path>"
python -m kb.cli extract
python -m kb.cli index
python -m kb.cli search "Endur configuration" --profile default
python -m kb.cli search "Endur configuration" --profile private
python -m kb.cli search "Endur configuration" --profile external-safe
```

### 18.4 First implementation constraints

Codex should:

- Avoid remote services initially unless explicitly configured.
- Not scan outside the KB root folder.
- Treat `00_Inbox` as the only intake source.
- Create YAML sidecars for each file.
- Use conservative classification defaults.
- Require approval before indexing uncertain documents.
- Generate a markdown review report.
- Store extracted text under `03_Extracted`.
- Store SQLite metadata under `04_Index`.
- Use SQLite FTS5 for initial keyword search.
- Add vector search later, not in the first prototype unless explicitly requested.

---

## 19. Example Codex Prompt

Use the following prompt later when starting the build:

```text
I want to build a local personal industry intelligence knowledge base for Endur / Findur / Openlink-related ETRM/CTRM documents.

Use the design in mike_gallagher_personal_industry_intelligence.md.

Create a Python CLI prototype that implements Option B: semi-automatic classification.

Initial requirements:
- Root folder: D:\Knowledge\Endur-KB
- Do not scan outside the root folder.
- Intake only from 00_Inbox.
- Create the recommended folder structure if missing.
- Extract text from PDF, DOCX, TXT, and MD files.
- Create YAML sidecar metadata files.
- Classify conservatively.
- Require review for vendor-authored, customer/partner-provided, unknown, or confidentiality-marked documents.
- Default unknown or sensitive documents to excluded from default search and external-safe search.
- Use SQLite for metadata.
- Use SQLite FTS5 for keyword search.
- Generate markdown review reports.
- Support search profiles: default, private, and external-safe.
- Keep vector search out of scope for the first version unless the design makes it easy to add later.

Start by proposing the file layout, Python dependencies, and implementation plan. Then create the code.
```

---

## 20. Future Enhancements

After the first prototype works, possible enhancements include:

### 20.1 Vector search

Add semantic search with Chroma, LanceDB, Qdrant, or another vector store.

### 20.2 Local web UI

Add a simple local web interface for:

- Intake review
- Search
- Metadata editing
- Reports
- Document preview

### 20.3 MCP integration

Expose the KB through controlled tools later, such as:

```text
kb_search(query, profile)
kb_read(document_id)
kb_list_recent()
kb_get_metadata(document_id)
kb_compare(document_id_a, document_id_b)
```

### 20.4 Remote access

Later options:

- VPN-only access
- Tailscale
- private web app
- self-hosted search service

Remote access should only be added after local security and search profiles are working.

### 20.5 Better document parsing

Add OCR and better extraction for scanned PDFs or image-heavy documents.

Potential tools:

- OCRmyPDF
- Tesseract
- Docling
- PyMuPDF

---

## 21. Final Recommended Starting Point

Start with the simplest useful version:

```text
D:\Knowledge\Endur-KB\
  00_Inbox\
  01_Source-Docs\
  02_My-Notes\
  03_Extracted\
  04_Index\
  05_Reports\
  99_Excluded\
```

Use:

- YAML metadata sidecars
- semi-automatic classification
- human approval before indexing
- three search profiles
- SQLite metadata
- SQLite FTS5 keyword search
- vector search later

This gives a clean foundation without over-structuring the knowledge base too early.


---

# Appendix A — Tooling Layout, Selection Rationale, and MCP Roles

This appendix clarifies how the major tools fit together and which parts should be custom-built versus reused.

The main design principle is:

> The Endur KB folder, metadata, and index should be the source of truth. Chat applications should be clients of the KB, not the owner of the KB.

## A.1 Architecture Summary

Recommended long-term layout:

```text
D:\Knowledge\Endur-KB\
  source files
  YAML metadata sidecars
  extracted text
  SQLite / FTS index
  optional vector index
        ↓
endur-kb-mcp-server
        ↓
MCP-capable client / chat front end
        ↓
LLM provider or local model
```

The recommended core architecture is:

```text
Endur KB folder + metadata + index
        ↓
Custom Endur KB MCP Server
        ↓
LibreChat, Claude Desktop, Cursor, Codex-compatible client, or another MCP client
```

Optional companion tools:

```text
Obsidian / VS Code
  for editing Markdown notes

AnythingLLM or Open WebUI
  for quick document-chat experiments

LlamaIndex
  for more advanced retrieval later

Docker Desktop
  for running self-hosted chat front ends such as LibreChat or Open WebUI
```

## A.2 MCP Terminology

MCP has two sides that must not be confused.

### MCP Server

The MCP server exposes data or capabilities.

For this project, the custom MCP server should expose the Endur KB through tools such as:

```text
kb_search(query, profile)
kb_read(document_id)
kb_get_metadata(document_id)
kb_list_documents(filters)
kb_recent()
kb_review_queue()
```

The MCP server should sit directly on top of:

```text
D:\Knowledge\Endur-KB\
  01_Source-Docs\
  02_My-Notes\
  03_Extracted\
  04_Index\
```

The MCP server is where the KB rules are enforced:

- default search
- private reference search
- external-output-safe search
- author filters
- source-category filters
- exclusion rules
- citation rules

### MCP Client

The MCP client is the app that connects to the MCP server.

Examples:

```text
LibreChat
AnythingLLM
Claude Desktop
Cursor
other MCP-capable AI clients
```

The MCP client does not own the knowledge base. It asks the MCP server to search or retrieve information.

### Practical meaning

When this document says **“build the MCP server,”** it means:

```text
Build endur-kb-mcp-server over D:\Knowledge\Endur-KB
```

When this document says **“use LibreChat or AnythingLLM with MCP,”** it means:

```text
LibreChat or AnythingLLM acts as the MCP client that calls endur-kb-mcp-server
```

## A.3 Component Responsibility Table

| Component | Role | MCP Role | Source of Truth? | Build or Install? |
|---|---|---:|---:|---|
| `D:\Knowledge\Endur-KB` | Document vault | None | Yes | Create manually |
| YAML sidecars | Metadata and policy | None | Yes | Build light tooling |
| SQLite / FTS5 | Local metadata and keyword index | None | Yes | Build light tooling |
| Optional vector index | Semantic retrieval | None | Partial | Add later |
| `endur-kb-mcp-server` | Controlled access layer | Server | No, but enforces access | Build |
| LibreChat | Chat UI / agent workbench | Client | No | Install later |
| AnythingLLM | Document-chat/RAG app | Client or standalone app | No | Optional |
| Open WebUI | Local chat/RAG UI | Client/tool platform, depending setup | No | Optional |
| Obsidian | Notes editor | None | No | Optional |
| VS Code | Editing/building | None | No | Install/use |
| LlamaIndex | RAG framework | None directly | No | Add later |
| LangChain/LangGraph | Agent/RAG framework | None directly | No | Optional later |

## A.4 Recommended Tool Choices

### A.4.1 Custom Endur KB MCP Server

#### Purpose

This is the central integration layer for the KB.

It should expose the KB to AI tools without giving those tools uncontrolled access to the file system.

#### Why it is recommended

The KB has specific rules that generic document-chat tools usually do not enforce well:

- vendor-authored documents may be private-reference only
- customer/partner-provided documents may be excluded from default search
- unknown-source documents may need review before indexing
- external-output-safe searches should only use approved material
- every document should have author/source metadata when known
- citations should include document title, filename, source category, and page/section where possible

These rules are specific to this project. They belong in a custom layer.

#### Strengths

- Gives precise control over search profiles.
- Keeps the KB independent of any one chat application.
- Lets multiple AI clients use the same KB.
- Preserves the folder and metadata design as the source of truth.
- Can be audited and tested.
- Allows clear separation between private reference and external-safe content.
- Can start simple with keyword search and add vector retrieval later.

#### Weaknesses

- Requires some development.
- Needs maintenance.
- Must be secured carefully if exposed remotely.
- Initial versions may be less polished than ready-made document-chat apps.
- MCP client support varies across tools.

#### Initial MCP tools to expose

```text
kb_search(query, profile="default", filters={})
kb_read(document_id, max_chars=None)
kb_get_metadata(document_id)
kb_list_documents(filters={})
kb_recent(limit=20)
kb_review_queue()
kb_explain_policy(document_id)
```

#### Future MCP tools

```text
kb_compare(document_id_a, document_id_b)
kb_summarize(document_id, profile="default")
kb_find_duplicates()
kb_reindex()
kb_classify_pending()
```

#### Design rule

The MCP server should not decide that a document is safe just because it exists. It should always obey metadata.

### A.4.2 LibreChat

#### Purpose

LibreChat is a self-hosted chat UI and AI workbench. It can act as an MCP client by connecting to configured MCP servers.

#### How it fits

Recommended role:

```text
LibreChat = front-end chat UI / MCP client
```

It should call:

```text
endur-kb-mcp-server
```

It should not own the Endur KB.

#### Why it is a strong candidate

LibreChat is useful if the goal is a ChatGPT-like local or self-hosted interface that can connect to models and MCP servers. LibreChat documentation describes configuring MCP servers in `librechat.yaml`, and using MCP servers either in chat or with agents.

#### Strengths

- Good fit for MCP-client use.
- ChatGPT-like interface.
- Supports multiple model providers.
- Supports agents and custom configuration.
- Better aligned with “use my MCP server” than pure document-chat tools.
- Can become the main UI after the KB MCP server works.

#### Weaknesses

- More installation complexity than a desktop app.
- Usually requires Docker or a self-hosted deployment.
- Configuration can be more involved.
- Not the simplest first POC.
- It is a client/front end, not the KB engine itself.
- Still depends on quality of the MCP server and retrieval layer.

#### When to install

Install LibreChat after:

1. The folder structure exists.
2. Metadata sidecars exist for a few documents.
3. The first simple local search works.
4. The MCP server exposes at least `kb_search` and `kb_read`.

LibreChat is not needed for the first file-ingestion POC.

### A.4.3 AnythingLLM

#### Purpose

AnythingLLM is a document-chat/RAG app. It is useful for quick experimentation with document Q&A.

#### How it fits

Recommended role:

```text
AnythingLLM = optional RAG/document-chat test bench
```

Possible uses:

- quickly chat with exported document folders
- validate whether a set of documents is useful
- compare retrieval results against the custom KB search
- prototype questions before investing in custom code

#### Important MCP clarification

AnythingLLM can act as an MCP client/host for MCP tools. Current documentation indicates AnythingLLM Desktop supports MCP Tools loading via MCP servers, but not MCP Resources, Prompts, or Sampling. That means it can call tools from the Endur KB MCP server, but it should not be treated as the full MCP platform for this KB.

#### Strengths

- Fastest route to basic “chat with documents.”
- Friendly UI.
- Useful for testing RAG behavior.
- Can use workspaces.
- Can help validate document usefulness before the custom MCP server is complete.
- Lower friction than building a full chat UI.

#### Weaknesses

- Not ideal as the source of truth for this KB.
- Its internal document indexing may not enforce the exact policy model required here.
- It may blur default/private/external-safe boundaries unless carefully separated.
- MCP support is useful but narrower than the full MCP concept.
- It can create a second copy/index of the KB, which may confuse governance if treated as authoritative.
- Less suitable than a custom MCP server for fine-grained metadata enforcement.

#### When to install

Optional early install if the goal is to test document Q&A quickly.

Use it with export folders, not the raw KB:

```text
D:\Knowledge\Endur-KB\06_Exports\default-search
D:\Knowledge\Endur-KB\06_Exports\private-reference
D:\Knowledge\Endur-KB\06_Exports\external-safe
```

Do not point it at the full KB until the metadata and policy model is mature.

### A.4.4 Open WebUI

#### Purpose

Open WebUI is a self-hosted AI interface with knowledge/RAG features and local-model orientation.

#### How it fits

Recommended role:

```text
Open WebUI = optional alternative chat/RAG front end
```

It may be useful if the project moves toward local models or a self-hosted AI interface.

#### Strengths

- Strong local/self-hosted AI community.
- Knowledge/RAG features.
- Local model support.
- Useful for testing local LLM workflows.
- Good if the objective includes non-cloud model experimentation.

#### Weaknesses

- Not the source-of-truth KB.
- RAG behavior and document management may not map cleanly to this project’s policy profiles.
- May require Docker and more operational setup.
- MCP/client fit may require more validation depending on the desired workflow.
- Could be redundant if LibreChat is already selected as the main UI.

#### When to install

Evaluate after LibreChat or instead of LibreChat if local-model workflows become more important than MCP-agent workflows.

### A.4.5 Obsidian

#### Purpose

Obsidian is a local Markdown note-taking app.

#### How it fits

Recommended role:

```text
Obsidian = optional editor for 02_My-Notes
```

#### Strengths

- Excellent for Markdown notes.
- Local-first.
- Easy linking between notes.
- Good for building a durable personal knowledge layer.
- Works well with Git if desired.
- Your own notes are the safest material for future external-facing use.

#### Weaknesses

- Not a RAG system.
- Not an MCP server.
- Not a document ingestion pipeline.
- Attachments and PDFs can become messy without discipline.
- Plugin ecosystem can create complexity.

#### When to install

Install early if you want a good environment for writing notes. Otherwise, VS Code is enough.

### A.4.6 VS Code

#### Purpose

VS Code is a practical editor for Markdown, YAML, Python, and project files.

#### How it fits

Recommended role:

```text
VS Code = build and maintenance environment
```

#### Strengths

- Good for editing Markdown and YAML.
- Good for Python development.
- Useful with Codex-style tools.
- Good diffing and Git integration.
- Lightweight enough for the first build.

#### Weaknesses

- Not a KB app by itself.
- Not a RAG system.
- Not an MCP client unless paired with extensions/tools that support MCP.

#### When to install

Use from the beginning.

### A.4.7 LlamaIndex

#### Purpose

LlamaIndex is a developer framework for building retrieval and RAG systems over private data.

#### How it fits

Recommended role:

```text
LlamaIndex = optional advanced retrieval framework later
```

For the first POC, SQLite FTS5 is enough. LlamaIndex becomes useful when the KB needs:

- semantic retrieval
- better chunking
- document nodes
- metadata-aware retrieval
- hybrid retrieval
- query routing
- source-aware summarization

#### Strengths

- Strong fit for custom RAG.
- Good abstraction for documents, nodes, indexes, retrievers, and query engines.
- Easier than building advanced RAG from scratch.
- Can preserve metadata filters if designed properly.

#### Weaknesses

- Adds framework complexity.
- Can hide important retrieval behavior.
- May be overkill for a small initial KB.
- Requires careful configuration to maintain policy boundaries.
- Version changes can affect implementation details.

#### When to install

Do not install for the first POC unless needed. Add after keyword search works and you have enough documents to justify semantic retrieval.

### A.4.8 LangChain / LangGraph

#### Purpose

LangChain and LangGraph are frameworks for building agentic workflows and more complex retrieval/application logic.

#### How they fit

Recommended role:

```text
LangChain/LangGraph = optional later, only if workflow complexity grows
```

#### Strengths

- Good for complex multi-step workflows.
- Strong ecosystem.
- Useful for tool orchestration.
- LangGraph can model stateful workflows.

#### Weaknesses

- More complexity than needed at the start.
- Easy to overbuild.
- Not necessary for basic ingestion, metadata, search, or MCP exposure.
- May distract from document hygiene and policy controls.

#### When to install

Only after the KB, search profiles, and MCP server are working and there is a clear need for complex workflows.

### A.4.9 SQLite / FTS5

#### Purpose

SQLite stores document metadata and provides local keyword search through FTS5.

#### How it fits

Recommended role:

```text
SQLite = first metadata and keyword search engine
```

#### Strengths

- Simple.
- Local.
- Easy to back up.
- Easy to inspect.
- Good for metadata filters.
- FTS5 supports exact keyword search.
- No separate server required.
- Perfect for a first POC.

#### Weaknesses

- Not semantic search by itself.
- Full-text ranking is basic compared to dedicated search engines.
- May need careful tuning for larger collections.
- Does not handle embeddings unless extended with another tool.

#### When to install

Use immediately in the first prototype.

### A.4.10 Vector Store

#### Purpose

A vector store enables semantic search.

Possible options:

```text
Chroma
LanceDB
Qdrant
SQLite vector extension
```

#### How it fits

Recommended role:

```text
Vector store = second-stage enhancement
```

#### Strengths

- Finds conceptual matches.
- Helps with broad questions and synonyms.
- Useful once the KB grows.
- Can be combined with metadata filters.

#### Weaknesses

- Adds complexity.
- Requires embeddings.
- Can retrieve plausible but wrong chunks if metadata filters are weak.
- Not enough by itself for legal/policy/source-sensitive search.
- Exact terms like Endur, Findur, Openlink, ION, module names, guide titles, and dates still need keyword search.

#### When to install

After:

1. There are enough documents to justify semantic search.
2. Metadata filters are working.
3. Keyword search is working.
4. Search profile enforcement is tested.

## A.5 Tooling Recommendation by Phase

| Phase | Install/Use | Avoid |
|---|---|---|
| POC 0 | File folders, VS Code | Anything complex |
| POC 1 | Python, PyYAML, SQLite FTS5, PDF/DOCX extractors | Vector DB |
| POC 2 | MCP Python SDK / FastMCP-style server | Full RAG framework |
| POC 3 | LibreChat as MCP client | Making LibreChat the KB |
| Optional validation | AnythingLLM | Treating AnythingLLM as authoritative |
| Advanced retrieval | LlamaIndex + vector store | Replacing metadata rules with embeddings |
| Remote access | Tailscale/VPN/reverse proxy later | Public exposure before auth/security |

## A.6 Recommended Final Tool Positioning

### Primary architecture

```text
Folder vault + metadata + SQLite
        ↓
Custom Endur KB MCP Server
        ↓
LibreChat or another MCP client
```

### Optional validation path

```text
Export folder
        ↓
AnythingLLM workspace
        ↓
Compare answers against MCP search
```

### Optional future advanced retrieval

```text
Extracted text + metadata
        ↓
LlamaIndex + vector store
        ↓
MCP server retrieval backend
```

---

# Appendix B — Enhanced Folder and Metadata Layout

This section enhances the earlier folder and metadata design while keeping the structure simple.

## B.1 Recommended Folder Layout

Use this as the starting point:

```text
D:\Knowledge\Endur-KB\
  00_Inbox\
  01_Source-Docs\
    Public\
    Vendor-Authored\
    Customer-Partner-Provided\
    Unknown-Needs-Review\
  02_My-Notes\
  03_Extracted\
  04_Index\
  05_Reports\
  06_Exports\
    default-search\
    private-reference\
    external-safe\
  07_Config\
  08_Templates\
  09_Logs\
  99_Excluded\
```

## B.2 Folder Purpose

### `00_Inbox`

Unreviewed files. The ingestion process scans only this folder for new source documents.

### `01_Source-Docs`

Approved or partially approved source documents.

Subfolders are intentionally shallow. Do not create a deep taxonomy yet.

### `02_My-Notes`

Your own analysis and notes. This is the safest long-term content for external use.

### `03_Extracted`

Generated extracted text/Markdown from source documents.

Do not manually edit unless there is a correction workflow.

### `04_Index`

SQLite database, FTS index, vector index if added later, ingestion state.

### `05_Reports`

Generated Markdown reports:

```text
intake-review-YYYY-MM-DD.md
classification-summary.md
external-safe-inventory.md
private-reference-inventory.md
unknown-source-review.md
```

### `06_Exports`

Generated export sets for external tools.

This folder lets tools like AnythingLLM or Open WebUI test a subset of documents without touching the master KB.

```text
06_Exports\
  default-search\
  private-reference\
  external-safe\
```

### `07_Config`

Configuration files for the ingestion and search system.

Examples:

```text
kb_config.yml
classification_rules.yml
search_profiles.yml
mcp_server_config.yml
```

### `08_Templates`

Templates for metadata and notes.

Examples:

```text
metadata-template.yml
endur-note-template.md
product-guide-summary-template.md
source-review-template.md
```

### `09_Logs`

Operational logs from ingestion, classification, indexing, and MCP server runs.

### `99_Excluded`

Files retained but intentionally excluded from indexing.

## B.3 Metadata Sidecar Naming

Use same basename as the source file:

```text
endur-product-guide.pdf
endur-product-guide.yml
```

For extracted text:

```text
03_Extracted\endur-product-guide.md
```

The YAML sidecar should reference both paths.

## B.4 Enhanced Metadata Template

```yaml
schema_version: 1

title:
author:
publisher:
source_category:
origin:
confidentiality:
usage_policy:

default_search: exclude
private_search: exclude
external_output_allowed: false

product:
  - Endur

topics: []

document_type:
language:
date_received:
date_published:
document_version:

source_path:
sidecar_path:
extracted_text_path:
original_filename:
file_extension:
file_size_bytes:
hash_sha256:

ingestion:
  status: needs_review
  classification_confidence: low
  review_required: true
  reviewed_by:
  reviewed_at:
  review_notes:
  last_indexed_at:

policy:
  allow_summary_for_private_use: true
  allow_direct_quotes: false
  allow_external_synthesis: false
  allow_external_direct_quotes: false
  require_citation: true

provenance:
  source_url:
  received_from:
  received_context:
  public_availability_verified: false
  public_availability_url:
  public_availability_checked_at:

extraction:
  method:
  page_count:
  word_count:
  extraction_quality: unknown
  ocr_required: false

notes:
```

## B.5 Search Profile Rules

Store search profiles in:

```text
07_Config\search_profiles.yml
```

Example:

```yaml
profiles:
  default:
    description: Normal Q&A using safe approved sources.
    include_when:
      default_search: include
    exclude_when:
      usage_policy:
        - do_not_index

  private:
    description: Private personal reference search.
    include_when:
      private_search: include
    exclude_when:
      usage_policy:
        - do_not_index

  external-safe:
    description: Search allowed for externally shareable content generation.
    include_when:
      external_output_allowed: true
    exclude_when:
      external_output_allowed: false
```

## B.6 Classification Rules File

Store classification rules in:

```text
07_Config\classification_rules.yml
```

Example:

```yaml
vendor_terms:
  - Openlink
  - OpenLink
  - ION
  - Endur
  - Findur

sensitive_terms:
  - confidential
  - proprietary
  - internal use only
  - customer confidential
  - NDA
  - not for distribution
  - restricted

default_policies:
  my_note:
    default_search: include
    private_search: include
    external_output_allowed: true

  public_vendor:
    default_search: include
    private_search: include
    external_output_allowed: true

  vendor_authored:
    default_search: exclude
    private_search: include
    external_output_allowed: false

  customer_partner_provided:
    default_search: exclude
    private_search: include
    external_output_allowed: false

  unknown:
    default_search: exclude
    private_search: exclude
    external_output_allowed: false
```

## B.7 Search Result Citation Format

Search results should return enough information for reliable answers:

```yaml
document_id:
title:
author:
publisher:
source_category:
usage_policy:
profile_used:
filename:
source_path:
page_number:
section_heading:
chunk_text:
```

Future AI answers should cite:

```text
Title — author/publisher — filename — page/section if available — search profile used
```

---

# Appendix C — Detailed Work Plan

This plan moves from a very simple proof of concept to a more complete MCP-backed KB.

## C.1 Phase 0 — Folder-Only POC

### Goal

Create the KB root and manually place a few test documents.

### Install

Required:

- VS Code or another editor

Optional:

- Obsidian for Markdown notes

### Steps

1. Create the root folder:

```powershell
mkdir D:\Knowledge\Endur-KB
```

2. Create simple subfolders:

```powershell
mkdir D:\Knowledge\Endur-KB\00_Inbox
mkdir D:\Knowledge\Endur-KB\01_Source-Docs
mkdir D:\Knowledge\Endur-KB\02_My-Notes
mkdir D:\Knowledge\Endur-KB\03_Extracted
mkdir D:\Knowledge\Endur-KB\04_Index
mkdir D:\Knowledge\Endur-KB\05_Reports
mkdir D:\Knowledge\Endur-KB\06_Exports
mkdir D:\Knowledge\Endur-KB\07_Config
mkdir D:\Knowledge\Endur-KB\08_Templates
mkdir D:\Knowledge\Endur-KB\09_Logs
mkdir D:\Knowledge\Endur-KB\99_Excluded
```

3. Add 5 to 10 test documents to `00_Inbox`.

4. Create 2 to 3 personal Markdown notes in `02_My-Notes`.

### Success criteria

- Folder structure exists.
- A few sample documents are in `00_Inbox`.
- A few personal notes exist.
- No indexing yet.

## C.2 Phase 1 — Manual Metadata POC

### Goal

Create sidecar YAML manually for a small set of files.

### Install

Required:

- VS Code
- YAML extension for VS Code, optional

### Steps

1. Create:

```text
08_Templates\metadata-template.yml
```

2. Manually create `.yml` sidecars for 5 documents.

3. Assign:

```yaml
source_category
origin
usage_policy
default_search
private_search
external_output_allowed
```

4. Manually move documents from `00_Inbox` to:

```text
01_Source-Docs\Public
01_Source-Docs\Vendor-Authored
01_Source-Docs\Customer-Partner-Provided
01_Source-Docs\Unknown-Needs-Review
```

### Success criteria

- 5 documents have YAML sidecars.
- At least one document is marked default-safe.
- At least one document is marked private-only.
- At least one document is marked excluded or needs review.

## C.3 Phase 2 — Python Ingestion POC

### Goal

Use Codex to build a simple Python CLI that scans `00_Inbox`, computes hashes, extracts basic metadata, and creates draft YAML sidecars.

### Install

Required:

- Python 3.11 or newer
- VS Code
- Git, optional but recommended

Python packages:

```text
pyyaml
python-docx
pypdf
pymupdf
typer
rich
```

Optional:

```text
pytest
```

### Steps

1. Ask Codex to create a Python project:

```text
endur_kb/
  kb/
    cli.py
    ingest.py
    classify.py
    extract.py
    metadata.py
    database.py
    reports.py
```

2. Implement:

```text
python -m kb.cli init --root "D:\Knowledge\Endur-KB"
python -m kb.cli scan
python -m kb.cli classify
python -m kb.cli review-report
```

3. For each inbox document, generate a draft YAML sidecar.

4. Generate a Markdown report:

```text
05_Reports\intake-review-YYYY-MM-DD.md
```

### Success criteria

- CLI scans `00_Inbox`.
- CLI creates draft metadata.
- CLI identifies likely vendor-authored documents.
- CLI identifies confidentiality markers.
- CLI creates a review report.
- No document is indexed without approval.

## C.4 Phase 3 — Approval and Extraction

### Goal

Add approval workflow and text extraction.

### Install

Same as Phase 2.

### Steps

1. Add command:

```text
python -m kb.cli approve --file "<path>"
```

2. Approved files move to the right source folder.

3. Extracted text is written to:

```text
03_Extracted\
```

4. YAML sidecar is updated with:

```yaml
ingestion.status: approved
extracted_text_path:
hash_sha256:
extraction.method:
extraction.word_count:
```

### Success criteria

- Approved documents are moved or copied out of `00_Inbox`.
- Extracted text files are created.
- Sidecar metadata references extracted text.
- Unknown or sensitive files remain unapproved unless manually confirmed.

## C.5 Phase 4 — SQLite Metadata and FTS5 Search

### Goal

Create a local searchable index using SQLite and FTS5.

### Install

Required:

- Python SQLite support, included with Python
- No separate database server required

### Steps

1. Create:

```text
04_Index\metadata.sqlite
```

2. Add tables:

```text
documents
document_chunks
document_chunks_fts
```

3. Chunk extracted text.

4. Add command:

```text
python -m kb.cli index
```

5. Add search command:

```text
python -m kb.cli search "Endur configuration" --profile default
python -m kb.cli search "Endur configuration" --profile private
python -m kb.cli search "Endur configuration" --profile external-safe
```

### Success criteria

- Search works locally.
- Search profile filtering works.
- Private-only documents do not appear in default search.
- Private-only documents do not appear in external-safe search.
- Search results include citation metadata.

## C.6 Phase 5 — First MCP Server POC

### Goal

Expose the KB through an MCP server.

### Install

Required:

- Python MCP SDK or a FastMCP-style Python framework

### Steps

1. Create project module:

```text
kb_mcp_server.py
```

2. Expose tools:

```text
kb_search
kb_read
kb_get_metadata
kb_list_documents
kb_recent
kb_review_queue
```

3. Test locally with an MCP inspector or compatible client.

4. Confirm that profile filters are enforced inside the MCP server.

### Success criteria

- MCP client can call `kb_search`.
- MCP client can call `kb_read`.
- `profile=default` excludes private-only documents.
- `profile=external-safe` excludes vendor-authored private-only documents.
- MCP server never returns excluded documents.

## C.7 Phase 6 — LibreChat as MCP Client

### Goal

Connect a real chat UI to the Endur KB MCP server.

### Install

Required:

- Docker Desktop
- LibreChat

### Steps

1. Install LibreChat.
2. Configure the Endur KB MCP server in `librechat.yaml`.
3. Restart LibreChat.
4. Test prompts:

```text
Search my Endur KB for configuration patterns using default search.
Search my Endur KB for configuration patterns using private reference search.
Find documents authored by Openlink but do not use them for external-safe output.
```

### Success criteria

- LibreChat can call the KB MCP server.
- Search results respect profiles.
- Answers cite sources.
- User can interact with the KB in a chat UI.

## C.8 Phase 7 — Optional AnythingLLM Validation

### Goal

Use AnythingLLM as a comparison tool, not as the source of truth.

### Install

Required only if validating RAG behavior:

- AnythingLLM Desktop or Docker version

### Steps

1. Generate export folders:

```text
06_Exports\default-search
06_Exports\private-reference
06_Exports\external-safe
```

2. Create separate AnythingLLM workspaces:

```text
Endur KB - Default
Endur KB - Private Reference
Endur KB - External Safe
```

3. Load only the appropriate export folder into each workspace.

4. Ask the same questions in AnythingLLM and LibreChat/MCP.

### Success criteria

- AnythingLLM helps validate retrieval quality.
- It does not become the authoritative KB.
- It does not bypass metadata rules.

## C.9 Phase 8 — Add Semantic / Vector Search

### Goal

Improve retrieval beyond keyword search.

### Install

Choose one:

```text
Chroma
LanceDB
Qdrant
```

Optional framework:

```text
LlamaIndex
```

### Steps

1. Add embeddings for approved chunks.
2. Store vector index under:

```text
04_Index\vector_index
```

3. Add hybrid search:

```text
keyword results + semantic results + metadata filters
```

4. Update MCP `kb_search` to support:

```text
search_mode: keyword | semantic | hybrid
```

### Success criteria

- Hybrid search improves broad conceptual questions.
- Metadata filters are applied before or during retrieval.
- External-safe policy still works.
- Exact keyword search remains available.

## C.10 Phase 9 — Local Web Review UI

### Goal

Make review and classification easier.

### Install

Choose one:

```text
FastAPI + simple HTML
Streamlit
NiceGUI
```

### Steps

1. Build a local review dashboard.
2. Show pending files.
3. Show extracted metadata.
4. Allow approve/edit/exclude.
5. Write YAML sidecars and update SQLite.

### Success criteria

- Review is easier than editing YAML manually.
- Every document still has an auditable metadata file.
- The UI does not replace the folder/metadata source of truth.

## C.11 Phase 10 — Remote Access Later

### Goal

Access the KB remotely after local security and policy are proven.

### Install

Options:

```text
Tailscale
WireGuard
VPN
reverse proxy with authentication
```

### Security rules

- Do not expose the MCP server publicly without authentication.
- Prefer VPN-only access.
- Keep the MCP server read-only at first.
- Log all remote access.
- Do not expose `99_Excluded`.
- Do not expose raw file system browsing.
- Keep external-safe mode as the default for any remote or public-facing workflow unless explicitly overridden.

### Success criteria

- Remote access works only over a trusted path.
- Search profiles still apply.
- Private-reference mode is protected.
- Logs are available.

---

# Appendix D — Recommended Build Sequence

## D.1 Minimal recommended path

```text
1. Create folder structure.
2. Create manual YAML metadata for 5 documents.
3. Build Python scanner/classifier.
4. Build extraction.
5. Build SQLite FTS5 search.
6. Build MCP server.
7. Connect LibreChat.
8. Optionally validate with AnythingLLM.
9. Add vector search.
10. Add remote access later.
```

## D.2 What not to do first

Do not start with:

- deep folder taxonomy
- vector database first
- remote access first
- public web server
- broad disk crawler
- fully automatic classification
- putting everything directly into AnythingLLM
- treating any chat app as the source of truth

## D.3 First practical POC target

The first real success target should be:

```text
I can place five Endur-related documents into 00_Inbox,
run a local command,
review suggested metadata,
approve two documents,
index them,
search them with profile filters,
and get cited results.
```

That is enough to prove the architecture before installing heavier tools.

---

# Appendix E — External Assumptions to Validate

The tool landscape changes quickly. Validate these assumptions before implementation.

## E.1 Assumptions to validate

1. LibreChat continues to support MCP server configuration through `librechat.yaml`.
2. LibreChat can use MCP servers in the chat area or through agents.
3. AnythingLLM continues to support MCP tool loading from MCP servers.
4. AnythingLLM's MCP support may be limited to Tools, not full Resources/Prompts/Sampling.
5. Open WebUI continues to support knowledge/RAG workflows.
6. The selected MCP Python SDK or FastMCP-style library is stable enough for local use.
7. The selected MCP client can pass profile/filter arguments reliably to the MCP server.
8. The selected vector store supports metadata filters well enough for search profiles.
9. The chosen deployment model does not require exposing the KB outside the local machine.

## E.2 Validation notes

As of the research performed for this document update:

- The MCP project describes servers as a way to expose tools, resources, and prompts to clients.
- LibreChat documentation describes MCP servers being configured in `librechat.yaml` and used in chat or with agents.
- AnythingLLM documentation states that Desktop supports MCP Tools loading from MCP servers, but not Resources, Prompts, or Sampling.
- Open WebUI documentation describes knowledge/RAG features for uploaded files and knowledge bases.

These facts should be rechecked before installation because these tools are evolving quickly.
