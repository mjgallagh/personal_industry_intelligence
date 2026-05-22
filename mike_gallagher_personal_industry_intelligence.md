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
