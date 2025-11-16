# Father Solanus Casey Hologram Project

This is the first full version of the pipeline. It is designed to be modular so that each component can be improved, swapped out, or rewritten as we learn more. Each phase of this system can operate as a standalone tool, whether for data extraction, document indexing, or voice and style mimicking. Later, it may be possible to unify these modules into a more streamlined or model driven workflow, but this initial architecture emphasizes clarity, flexibility, and repeatability.

The data extraction pipeline can serve archivists or researchers looking for machine readable versions of historical texts. The indexer can operate independently as a searchable digital library for scholars. The mimicker can be used as its own stylistic transformation tool. Together, they form the full hologram system, but each part has value on its own.

---

## Overview

### Where we are

We currently have the publicly available writings of Father Solanus Casey ([https://www.solanuscasey.org/about-blessed-solanus-casey/writings-of-solanus/](https://www.solanuscasey.org/about-blessed-solanus-casey/writings-of-solanus/)) in PDF format.

### Where we want to go

Our goal is to create a holographic representation of Father Solanus Casey that:

* Speaks in his voice and style
* Responds using information grounded in his actual writings
* Operates through a combined pipeline of indexing, retrieval, transformation, and generation

This first major document describes the entire workflow from raw PDFs to the interactive hologram.

---

# Part One: The Pipeline for the Corpus

This is the pipeline for converting the PDFs into structured, machine readable data. These steps must be done with extreme accuracy because the entire system depends on this foundation.

---

## Phase 1: Initial Data Extraction

### Starting point

Raw PDF files.

### Goal

Page-by-page text extraction with as much fidelity to the original as possible.

### Method

1. Extract text from each PDF using Python tools (such as pdfplumber or PyPDF2).
2. Use OCR as a fallback layer to ensure no text is missed.
3. Spot check multiple files manually.
4. If both native extraction and OCR are available, compare them using an LLM.
5. Fix formatting issues via repeated LLM passes. Running each page or chunk **three times** allows cross examination:

   * If two match, accept
   * If all differ, manually review

Accurate extraction is essential. This phase builds the base that all later phases rely on.

---

## Phase 2: Break the Pages into Individual Documents

### Starting point

Clean page-by-page text.

### Goal

Document-by-document text segments.

### Why this matters

Documents span unpredictable lengths. Some are short notes. Some are multi page letters. Each must become its own document for indexing.

### Method (three step approach)

1. Identify major boundaries. These are notebook level or volume level transitions, where indices or table of contents can help.
2. Identify document boundaries inside each major section. Use a multistage LLM approach:

   * Sliding window scans to see where documents likely begin and end.
   * Local checks comparing page endings and beginnings.
   * Larger window summary passes for consistency.
3. Extract each document as its own standalone file containing the full text.

### Importance

We cannot redo this step later. Correct segmentation is vital for:

* Historical fidelity
* Proper retrieval
* Future analysis and modeling

---

# Part Two: The Indexer Path

This branch is responsible for turning the corpus into a powerful searchable tool.

---

## Phase 1 Indexer: Data Formatting

### Starting point

Document-by-document text files.

### Goal

Transform each document into a standardized machine readable JSON file that:

* Includes high level metadata
* Preserves original structure where helpful
* Ensures consistent fields

### Metadata fields

Examples include:

* Document type (letter, note, sermon, reflection, prayer, etc.)
* Date (year, month, day if available)
* Source PDF
* Document identifier or title
* The full text itself

### Method

Use an LLM to:

1. Parse the text.
2. Extract metadata.
3. Format all data into a fixed JSON template.
4. Run multiple passes for consistency and discrepancy resolution.

---

## Phase 2 Indexer: Semantic Breakdowns

### Starting point

JSON documents with basic metadata.

### Goal

Enrich metadata with deeper semantic information.

### Examples of semantic fields

* Recipient (for letters)
* Occasion or event
* Themes or topics addressed
* Named individuals mentioned
* Locations referenced
* Spiritual themes (e.g. suffering, healing, prayer, vocation)

### Method

LLM based semantic extraction applied per document.
Multiple passes help ensure correctness.

---

## Phase 3 Indexer: Loading the Enriched Corpus into Azure AI Search

### Starting point

Complete enriched JSON files.

### Goal

Build a robust search index that powers retrieval for our system.

### What we will index

Each JSON document contributes:

* Full text
* Document type
* Date fields
* People mentioned
* Source details
* Semantic info: topics, recipient, context, etc.

All fields become searchable and filterable.

### How we build the indexer

1. Define a schema in Azure AI Search specifying:

   * Searchable fields
   * Filterable fields
   * Sortable fields
   * A vector embedding field
2. Generate embeddings for each document.
3. Upload JSON documents and metadata.
4. Attach embedding vectors.

### Example of a JSON document

```json
{
  "document_id": "SC_001_1941_07_15",
  "document_type": "letter",
  "year": 1941,
  "month": 7,
  "day": 15,
  "source_pdf": "volume_1.pdf",
  "source_document_name": "Letter to Mrs. Donovan",
  "text": "Dear Mrs. Donovan, thank you for your kind note...",
  "semantic": {
    "recipient": "Mrs. Donovan",
    "topics": ["gratitude", "family", "prayer"],
    "occasion": "reply to personal note",
    "mentioned_people": ["Donovan family"]
  },
  "embedding": "[vector omitted]"
}
```

### Why this matters

This index powers:

* Retrieval augmented generation
* Historical queries
* Topic specific exploration
* Future reranking or custom search modules

---

# Part Three: The Mimicker Path

This branch constructs the model capable of speaking like Father Solanus Casey.

---

## Phase 1 Mimicker: LLM Sanitization

### Goal

Create parallel data for training the stylistic model.

### Method

1. Take each document.
2. Create a sanitized version rewritten in generic LLM style.
3. These become input and output pairs:

   * Input: sanitized LLM text
   * Output: original Solanus Casey text

This ensures the model learns the transformation from LLM speech to Solanus style.

---

## Phase 2 Mimicker: Model Training

### Goal

Train a model that transforms ordinary LLM style into Solanus Casey style.

### Method

1. Use the sanitized to original pairs as training data.
2. Train on a local GPU using a small to medium language model.
3. Evaluate using held out samples.

### Result

A component that:

* Accepts ordinary text responses
* Rewrites them to sound like Solanus Casey

---

# Part Four: Combining the Indexer and Mimicker

### Workflow

1. User asks a question.
2. A general LLM interprets the question.
3. The indexer retrieves relevant passages from the corpus.
4. The LLM constructs an answer using the retrieved texts.
5. The mimicker rewrites the answer into Solanus Casey's voice.

This forms the complete intelligent text pipeline.

---

# Part Five: Voice Mimicker

### Goal

Convert text written in Solanus style into audio that resembles his speaking voice.

### Approach

1. Collect any available audio recordings of Father Solanus Casey. If none exist, choose a high quality synthetic voice that matches period appropriate tone and simplicity.
2. Use existing text to speech engines that support voice cloning or fine voice stylization.
3. Feed the text outputs from the mimicker model to generate speech.
4. Post process as needed to adjust pacing, warmth, and oral style.

This yields spoken answers in a voice that aligns with his persona.

---

# Part Six: Hologram Assembly

### Goal

Create a holographic front end for the combined system.

### Approach

1. Use a motion capture based or template based avatar system.
2. Connect real time audio from the mimicker voice into lip synced animation.
3. Use gaze tracking and simple gesture animations to give a natural appearance.
4. Display using:

   * Rear projection into a hologram fan
   * Pepper's ghost style projection
   * AR headset

Behind the scenes, the avatar is simply reading the output from the combined pipeline.

---

# End State

Once all pieces are assembled, we will have:

* A complete digital corpus of Solanus Casey's writings
* A powerful indexer for grounded responses
* A trained mimicker for stylistic speech
* A voice engine for oral delivery
* A holographic avatar to represent him visually

This forms the complete Father Solanus Casey hologram experience.
