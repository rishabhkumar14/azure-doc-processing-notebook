# Choose the Right Azure Foundry Tool for Document Processing

A hands-on notebook that runs the same document through all three Azure AI document processing approaches — side-by-side — so you can see the fundamental difference between *seeing text*, *structuring content*, and *understanding meaning*.

---

## Overview

**File:** `choose_right_foundry_tool_doc_processing.ipynb`

This notebook demonstrates how to choose the right Azure AI tool for document processing by comparing three approaches on a single document (`PeriodicTable.pdf`). Each approach serves as a different **lens** for understanding documents, and they're complementary rather than mutually exclusive.

### The Three Lenses

| Service | Core Question | Best For |
|---|---|---|
| **Document Intelligence** | Where is the text and what fields are present? | Standard forms, invoices, receipts, IDs, contracts — high-accuracy OCR with confidence scores |
| **Content Understanding** | What does this content *mean* in context? | Complex layouts, semantic document structure, paragraph role classification, figure detection |
| **Azure OpenAI (Custom Models)** | How should I reason and act on this content? | Custom workflows, narrative generation, agentic pipelines, experimental scenarios |

---

## Architecture

```
📄 PeriodicTable.pdf
        │
        ▼
🔍 Document Intelligence
        │
        ├── 📄 OCR/Read → Clean text in reading order
        ├── 📊 Layout → Tables + paragraphs + bounding boxes
        └── 🧠 Content Understanding → Semantic roles + figures (via markdown)

Optional: ✨ Azure OpenAI (GPT-4o) → Custom vision analysis
```

---

## Prerequisites

### Azure Resources

- **Azure Document Intelligence** resource
  - FormRecognizer (Document Intelligence) resource
  - Custom subdomain enabled (required for token authentication)
  - Location: eastus (or your preferred region)
- **Azure CLI** installed and authenticated (`az login`)
- **Optional:** Azure OpenAI resource (for Cell 13)

### Python Packages

```bash
pip install azure-ai-documentintelligence azure-core openai pandas requests Pillow
```

### Local Files

- `PeriodicTable.pdf` in the same directory as the notebook

---

## Quick Start

### 1. Create Azure Resources

```bash
# Login to Azure
az login

# Create resource group
az group create --name rg-doc-processing-demo --location eastus

# Create Document Intelligence resource
az cognitiveservices account create \
  --name di-demo-keys \
  --resource-group rg-doc-processing-demo \
  --kind FormRecognizer --sku S0 --location eastus \
  --custom-domain di-demo-keys --yes

# Get access token (valid for ~1 hour)
az account get-access-token --resource https://cognitiveservices.azure.com --query accessToken -o tsv
```

### 2. Configure the Notebook

Update **Cell 3** with your resource details:

```python
RESOURCE_GROUP = "rg-doc-processing-demo"
DI_RESOURCE = "di-demo-keys"
DI_ENDPOINT = "https://di-demo-keys.cognitiveservices.azure.com/"
ACCESS_TOKEN = "your-access-token-here"  # From az login command above
```

### 3. Run the Notebook

Execute cells in order:

1. **Cell 2** — Install Python dependencies
2. **Cell 3** — Configure Azure resources and authentication
3. **Cell 4** — Initialize Document Intelligence client
4. **Cell 5** — Verify document exists
5. **Cells 7-9** — Document Intelligence demonstrations (OCR/Read → Layout → Tables)
6. **Cell 11** — Content Understanding with markdown output
7. **Cell 13** — (Optional) Azure OpenAI custom analysis
8. **Cells 14-15** — Comparison tables and decision guidance

---

## What You'll Learn

### Document Intelligence Features

- **OCR/Read Model** — Extract clean text in reading order with character/line/word detection
- **Layout Model** — Detect tables, paragraphs, and page regions with bounding boxes
- **Confidence Scores** — Understand reliability metrics for quality assurance
- **Table Extraction** — Convert detected tables to pandas DataFrames

### Content Understanding (via Markdown Output)

- **Semantic Analysis** — Paragraph role classification (titles, headers, footers, page numbers)
- **Figure Detection** — Identify and describe figures within documents
- **Enhanced Tables** — Handle merged cells (colspan/rowspan) correctly
- **Structured Markdown** — Output formatted for document modernization and CMS integration

### Azure OpenAI Integration

- **Custom Vision Analysis** — Use GPT-4o for flexible document reasoning
- **Prompt Engineering** — Control analysis depth and output format
- **Agentic Workflows** — Build custom document processing pipelines

---

## Key Insights

### When to Use Each Tool

**Document Intelligence:**
- ✅ Standard forms with known structure (invoices, receipts, contracts)
- ✅ High-volume batch processing with consistent document types
- ✅ Scenarios requiring confidence scores and bounding box grounding
- ✅ RAG pipelines needing clean text extraction

**Content Understanding:**
- ✅ Complex document layouts with varying structures
- ✅ Document modernization projects (legacy docs → markdown)
- ✅ Content management system integration
- ✅ Semantic understanding of document roles (headers, footers, sections)

**Azure OpenAI:**
- ✅ Custom reasoning and inference beyond extraction
- ✅ Narrative output generation
- ✅ Agentic document workflows
- ✅ Experimental scenarios requiring prompt flexibility

### Best Practices

**✅ Do:**
- Use **prebuilt models** for standard document types (invoice, receipt, ID)
- Leverage **confidence scores** to route low-confidence items to human review
- Use **Read** for simple text extraction (faster than Layout)
- Enable **markdown output** (`output_content_format="markdown"`) for semantic understanding
- Process specific pages with `pages` parameter to reduce latency

**❌ Avoid:**
- Expecting inference from Document Intelligence (it extracts visible content, not implied meaning)
- Using Layout for simple text-only extraction
- Ignoring confidence scores
- Processing documents without validation workflows

---

## Notebook Structure

| Cell | Purpose | Type |
|---|---|---|
| 1 | Introduction and architecture overview | Markdown |
| 2 | Install Python dependencies | Code |
| 3 | Configure Azure resources and authentication | Markdown/Code |
| 4 | Initialize Document Intelligence client | Code |
| 5 | Verify document exists | Code |
| 7-9 | Document Intelligence: Read, Layout, Tables | Markdown/Code |
| 11 | Content Understanding with markdown output | Code |
| 13 | (Optional) Azure OpenAI vision analysis | Code |
| 14 | Side-by-side comparison table | Markdown |
| 15 | Decision guide and best practices | Markdown |

---

## Authentication

This notebook uses **Azure CLI token authentication** (no API keys needed):

```bash
# Get a fresh token (valid for ~1 hour)
az account get-access-token --resource https://cognitiveservices.azure.com --query accessToken -o tsv
```

The token is wrapped in a simple `TokenCredential` class compatible with the Azure SDK.

---

## Output Examples

### Document Intelligence Layout
- Detects **tables** with row/column structure
- Extracts **paragraphs** with bounding box coordinates
- Provides **confidence scores** for each element

### Content Understanding
- Classifies **paragraph roles** (title, section heading, page header, footer)
- Detects **figures** with semantic descriptions
- Outputs **structured markdown** with proper heading hierarchy

### Azure OpenAI
- Generates **custom narratives** based on document content
- Performs **reasoning and inference** beyond extraction
- Enables **agentic workflows** with tool calling

---

## References

- [Choose the Right AI Tool — Microsoft Docs](https://learn.microsoft.com/en-us/azure/ai-services/content-understanding/choosing-right-ai-tool)
- [Azure AI Document Intelligence](https://learn.microsoft.com/en-us/azure/ai-services/document-intelligence/)
- [Azure AI Content Understanding](https://learn.microsoft.com/en-us/azure/ai-services/content-understanding/)
- [Azure OpenAI Service](https://learn.microsoft.com/en-us/azure/ai-services/openai/)

---

## Troubleshooting

### Token Expired
If you see authentication errors, refresh your token:
```bash
az account get-access-token --resource https://cognitiveservices.azure.com --query accessToken -o tsv
```
Update `ACCESS_TOKEN` in Cell 3 and re-run Cell 4.

### Document Not Found
Ensure `PeriodicTable.pdf` is in the same directory as the notebook.

### Custom Subdomain Required
Token authentication requires a custom subdomain. Create your resource with `--custom-domain` flag as shown in Quick Start.

---

## License

This notebook is for educational and demonstration purposes.
