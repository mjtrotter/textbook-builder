# Build Process: From Sources to Finished Textbook

Complete documentation of the compilation pipeline that transforms source materials into professionally formatted educational content.

---

## Build Pipeline Overview

```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│   SOURCES   │ →  │  ANALYSIS   │ →  │  GENERATION │ →  │   BUILD     │
│             │    │   PHASE     │    │    PHASE    │    │   PHASE     │
│ PDFs, docs, │    │ Parse, map, │    │ Draft, edit,│    │ Format,     │
│ papers, etc │    │ structure   │    │ illustrate  │    │ compile     │
└─────────────┘    └─────────────┘    └─────────────┘    └─────────────┘
                                                              │
                                                              ▼
                                                        ┌─────────────┐
                                                        │   OUTPUT    │
                                                        │ PDF, EPUB,  │
                                                        │ Print-ready │
                                                        └─────────────┘
```

---

## Phase 1: Source Ingestion & Processing

### Supported Source Formats

| Format | Parser | Notes |
|--------|--------|-------|
| PDF | PyMuPDF, pdfplumber | OCR fallback for scanned docs |
| EPUB | ebooklib | Preserve structure |
| DOCX | python-docx | Tables, images extracted |
| LaTeX | TexSoup | Mathematical content |
| Markdown | mistune | Code blocks, tables |
| HTML | BeautifulSoup | Clean web content |
| Images | OCR (Tesseract) | Tables, diagrams with text |
| PPT/PPTX | python-pptx | Slide content, notes |

### Ingestion Pipeline

```python
# Pseudo-code for ingestion
def ingest_source(source_path):
    # 1. Detect format
    format = detect_format(source_path)

    # 2. Parse content
    raw_content = parsers[format].parse(source_path)

    # 3. Extract structure
    structure = extract_structure(raw_content)
    # - Headings hierarchy
    # - Lists and tables
    # - Figures and captions
    # - References and citations

    # 4. Extract assets
    assets = extract_assets(raw_content)
    # - Images
    # - Equations
    # - Code blocks
    # - Tables

    # 5. Create normalized document
    return NormalizedDocument(
        content=structure,
        assets=assets,
        metadata=extract_metadata(raw_content)
    )
```

### Content Normalization

All sources are converted to internal normalized format:

```json
{
  "metadata": {
    "title": "...",
    "author": "...",
    "date": "...",
    "source_type": "pdf|epub|docx|...",
    "quality_score": 0.85
  },
  "structure": {
    "sections": [
      {
        "id": "sec_001",
        "level": 1,
        "title": "Chapter Title",
        "content": "...",
        "children": [...]
      }
    ]
  },
  "assets": {
    "images": [...],
    "equations": [...],
    "tables": [...],
    "code_blocks": [...]
  },
  "references": [...]
}
```

---

## Phase 2: Content Analysis & Mapping

### Concept Extraction

```python
def extract_concepts(normalized_docs):
    concepts = []

    for doc in normalized_docs:
        # Named entity recognition
        entities = ner_model.extract(doc.content)

        # Key term extraction
        terms = extract_key_terms(doc.content)

        # Definition detection
        definitions = detect_definitions(doc.content)

        # Relationship extraction
        relationships = extract_relationships(entities)

        concepts.extend(
            merge_concepts(entities, terms, definitions, relationships)
        )

    return deduplicate(concepts)
```

### Knowledge Graph Construction

```
Nodes: Concepts, terms, definitions
Edges: Prerequisites, related_to, part_of, example_of

Example:
    [Photosynthesis] --requires--> [Chlorophyll]
    [Chlorophyll] --located_in--> [Chloroplast]
    [Photosynthesis] --produces--> [Glucose]
    [Glucose] --example_of--> [Carbohydrate]
```

### Coverage Analysis

```python
def analyze_coverage(knowledge_graph, curriculum_standards):
    covered = set()
    gaps = set()

    for standard in curriculum_standards:
        if knowledge_graph.covers(standard):
            covered.add(standard)
        else:
            gaps.add(standard)

    return CoverageReport(
        covered=covered,
        gaps=gaps,
        coverage_percentage=len(covered) / len(curriculum_standards)
    )
```

---

## Phase 3: Content Generation

### Draft Generation Pipeline

```
┌──────────────────────────────────────────────────────────────┐
│                    DRAFTING LOOP                              │
│                                                               │
│  ┌─────────┐   ┌─────────┐   ┌─────────┐   ┌─────────┐       │
│  │ Writer  │ → │Technical│ → │Pedagogy │ → │ Style   │       │
│  │ Agent   │   │ Review  │   │ Review  │   │ Editor  │       │
│  └─────────┘   └─────────┘   └─────────┘   └─────────┘       │
│       ↑                                         │             │
│       └─────────── Synthesis Agent ←────────────┘             │
│                   (iterate until pass)                        │
└──────────────────────────────────────────────────────────────┘
```

### Generation Configuration

```yaml
drafting:
  max_iterations: 5
  consensus_threshold: 0.85

  agents:
    writer:
      model: claude-3-sonnet
      temperature: 0.7

    technical_reviewer:
      model: claude-3-opus
      temperature: 0.3

    pedagogical_reviewer:
      model: claude-3-sonnet
      temperature: 0.5

    style_editor:
      model: claude-3-haiku
      temperature: 0.3

  quality_gates:
    technical_accuracy: 0.9
    readability_score: target ± 5
    learning_objective_coverage: 1.0
```

### Content Templates

#### Chapter Template
```markdown
# Chapter {{chapter_num}}: {{title}}

## Learning Objectives
By the end of this chapter, you will be able to:
{{#each objectives}}
- {{this}}
{{/each}}

## Prerequisites
{{prerequisites}}

## Introduction
{{introduction}}

{{#each lessons}}
## Lesson {{@index}}: {{title}}

### Key Concepts
{{concepts}}

### Main Content
{{content}}

### Worked Examples
{{#each examples}}
#### Example {{@index}}
{{this}}
{{/each}}

### Practice Exercises
{{exercises}}

### Lesson Summary
{{summary}}

{{/each}}

## Chapter Review
{{review}}

## Chapter Exercises
{{#each exercises}}
### {{difficulty}}
{{problems}}
{{/each}}

## Chapter Assessment
{{assessment}}
```

---

## Phase 4: Visualization Pipeline

### Visual Needs Detection

```python
def detect_visual_needs(content):
    visual_flags = []

    # Pattern-based detection
    patterns = {
        'process': r'(steps|process|procedure|workflow)',
        'comparison': r'(compare|contrast|versus|difference)',
        'structure': r'(parts|components|structure|anatomy)',
        'data': r'(data|results|percentage|statistics)',
        'timeline': r'(history|evolution|timeline|chronolog)',
        'relationship': r'(relationship|connection|cause|effect)',
        'example': r'(example|illustration|diagram|figure)'
    }

    for visual_type, pattern in patterns.items():
        matches = find_matches(content, pattern)
        for match in matches:
            visual_flags.append(VisualNeed(
                location=match.location,
                type=visual_type,
                context=match.context,
                priority=calculate_priority(match)
            ))

    return visual_flags
```

### Image Generation Pipeline

```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│   Visual    │ →  │   Prompt    │ →  │   Image     │
│   Need      │    │ Generation  │    │ Generation  │
└─────────────┘    └─────────────┘    └─────────────┘
                                             │
                                             ▼
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│   Final     │ ←  │   Quality   │ ←  │   Review    │
│   Image     │    │   Check     │    │   & Edit    │
└─────────────┘    └─────────────┘    └─────────────┘
```

### Image Specifications

```yaml
images:
  format: PNG
  resolution: 300 DPI
  color_space: RGB (for digital), CMYK (for print)

  dimensions:
    full_page: 2550x3300 px  # 8.5x11 at 300dpi
    half_page: 2550x1650 px
    quarter_page: 1275x1650 px
    inline: 600x400 px max

  style:
    educational: true
    consistent_palette: true
    accessible_colors: true  # Colorblind-safe

  requirements:
    - Clear labels
    - Appropriate scale indicators
    - High contrast
    - No watermarks
    - Educational accuracy verified
```

### Caption & Alt-Text Generation

```python
def generate_caption_and_alt(image, context):
    caption = caption_agent.generate(
        image=image,
        context=context,
        format="Figure {num}: {description} ({source})"
    )

    alt_text = alt_text_agent.generate(
        image=image,
        purpose="educational",
        detail_level="comprehensive",
        max_length=250
    )

    return caption, alt_text
```

---

## Phase 5: Assessment Generation

### Question Generation Pipeline

```python
def generate_assessments(content, learning_objectives, config):
    assessments = {
        'formative': [],
        'summative': []
    }

    for objective in learning_objectives:
        # Generate questions at multiple Bloom's levels
        for bloom_level in config.bloom_levels:
            questions = question_generator.generate(
                content=content,
                objective=objective,
                bloom_level=bloom_level,
                question_types=config.question_types
            )

            # Generate answer key
            for question in questions:
                question.answer_key = answer_generator.generate(question)
                question.rubric = rubric_generator.generate(question)
                question.distractors = distractor_generator.generate(question)

            assessments[config.assessment_type].extend(questions)

    return assessments
```

### Question Type Templates

#### Multiple Choice
```json
{
  "type": "multiple_choice",
  "stem": "What is the primary function of mitochondria?",
  "options": [
    {"id": "A", "text": "Protein synthesis", "is_correct": false},
    {"id": "B", "text": "Energy production", "is_correct": true},
    {"id": "C", "text": "Cell division", "is_correct": false},
    {"id": "D", "text": "Waste removal", "is_correct": false}
  ],
  "bloom_level": "remember",
  "difficulty": "basic",
  "explanation": "Mitochondria are often called the powerhouse of the cell..."
}
```

#### Free Response
```json
{
  "type": "free_response",
  "prompt": "Explain how photosynthesis and cellular respiration are related processes.",
  "bloom_level": "analyze",
  "difficulty": "intermediate",
  "rubric": {
    "criteria": [
      {"name": "Identifies inputs/outputs", "points": 2},
      {"name": "Explains relationship", "points": 3},
      {"name": "Uses correct terminology", "points": 2},
      {"name": "Provides examples", "points": 3}
    ],
    "total_points": 10
  },
  "sample_answer": "..."
}
```

---

## Phase 6: Document Assembly

### Assembly Pipeline

```python
def assemble_document(chapters, config):
    document = Document(config)

    # Front matter
    document.add(generate_title_page(config))
    document.add(generate_copyright_page(config))
    document.add(generate_toc(chapters))
    document.add(generate_preface(config))

    # Main content
    for chapter in chapters:
        document.add(chapter.content)
        document.add(chapter.images)
        document.add(chapter.exercises)
        document.add(chapter.assessment)

    # Back matter
    document.add(generate_appendices(config))
    document.add(generate_glossary(chapters))
    document.add(generate_index(chapters))
    document.add(generate_bibliography(chapters))
    document.add(generate_answer_key(chapters))

    return document
```

### Document Structure

```
Front Matter
├── Half-title page
├── Title page
├── Copyright page
├── Dedication (optional)
├── Table of Contents
├── List of Figures
├── List of Tables
├── Preface
├── Acknowledgments
└── Introduction

Main Content
├── Part I (optional grouping)
│   ├── Chapter 1
│   │   ├── Opening elements
│   │   ├── Lessons
│   │   ├── Exercises
│   │   └── Assessment
│   ├── Chapter 2
│   └── ...
├── Part II
└── ...

Back Matter
├── Appendices
├── Glossary
├── Index
├── Bibliography/References
├── Answer Key
└── About the Author (optional)
```

---

## Phase 7: Formatting & Typesetting

### Typography Application

```python
def apply_typography(document, style_config):
    # Apply fonts
    document.set_body_font(style_config.body_font)
    document.set_heading_fonts(style_config.heading_fonts)

    # Apply sizing
    document.set_font_sizes({
        'body': style_config.body_size,
        'h1': style_config.h1_size,
        'h2': style_config.h2_size,
        'h3': style_config.h3_size,
        'caption': style_config.caption_size,
        'footnote': style_config.footnote_size
    })

    # Apply spacing
    document.set_line_height(style_config.line_height)
    document.set_paragraph_spacing(style_config.para_spacing)

    # Apply margins
    document.set_margins(style_config.margins)

    return document
```

### Style Configuration by Level

```yaml
# Elementary
elementary:
  body_font: "Comic Sans MS"
  body_size: 14pt
  line_height: 1.8
  margins: {top: 1in, bottom: 1in, inner: 0.75in, outer: 0.75in}

# Middle School
middle_school:
  body_font: "Open Sans"
  body_size: 12pt
  line_height: 1.5
  margins: {top: 0.75in, bottom: 0.75in, inner: 0.75in, outer: 0.5in}

# High School
high_school:
  body_font: "Georgia"
  body_size: 11pt
  line_height: 1.3
  margins: {top: 0.75in, bottom: 0.75in, inner: 0.75in, outer: 0.5in}

# College
college:
  body_font: "Garamond"
  body_size: 10pt
  line_height: 1.2
  margins: {top: 0.75in, bottom: 0.75in, inner: 1in, outer: 0.5in}
```

### Layout Engine

```python
def layout_pages(document):
    pages = []
    current_page = Page()

    for element in document.elements:
        # Check if element fits on current page
        if not current_page.can_fit(element):
            # Handle page break
            pages.append(current_page)
            current_page = Page()

            # Avoid widows/orphans
            if is_widow_orphan_situation(element):
                adjust_previous_page(pages[-1])

        # Place element
        current_page.add(element)

        # Handle floating elements (figures, tables)
        if element.is_float:
            position = find_optimal_position(element, current_page)
            current_page.place_float(element, position)

    return pages
```

---

## Phase 8: Output Generation

### Output Formats

#### Print-Ready PDF
```python
def generate_print_pdf(document):
    # Convert colors to CMYK
    document.convert_colors('CMYK')

    # Ensure 300 DPI
    document.set_resolution(300)

    # Add bleed
    document.add_bleed(0.125)  # inches

    # Add crop marks
    document.add_crop_marks()

    # Export PDF/X-1a
    return document.export('PDF/X-1a')
```

#### EPUB3
```python
def generate_epub(document):
    epub = EPUB3()

    # Create navigation
    epub.set_toc(document.toc)
    epub.set_landmarks(document.landmarks)

    # Add content
    for chapter in document.chapters:
        # Convert to XHTML
        xhtml = convert_to_xhtml(chapter)
        epub.add_content(xhtml)

    # Add images
    for image in document.images:
        # Convert to RGB, optimize size
        optimized = optimize_for_ebook(image)
        epub.add_image(optimized)

    # Add CSS
    epub.add_stylesheet(generate_responsive_css())

    # Add metadata
    epub.set_metadata(document.metadata)

    return epub.export()
```

#### Kindle (MOBI/KF8)
```python
def generate_kindle(document):
    # First generate EPUB
    epub = generate_epub(document)

    # Convert using Kindlegen or KindleCreate
    kindle = convert_epub_to_kindle(epub)

    # Add Kindle-specific features
    kindle.add_xray_data(document.index)
    kindle.enable_text_to_speech()

    return kindle
```

### Quality Assurance Checks

```python
def run_qa_checks(document):
    checks = []

    # Typography checks
    checks.append(check_widows_orphans(document))
    checks.append(check_hyphenation(document))
    checks.append(check_line_lengths(document))
    checks.append(check_font_consistency(document))

    # Content checks
    checks.append(check_cross_references(document))
    checks.append(check_index_entries(document))
    checks.append(check_toc_accuracy(document))
    checks.append(check_figure_numbering(document))

    # Technical checks
    checks.append(check_image_resolution(document))
    checks.append(check_color_space(document))
    checks.append(check_accessibility(document))

    # Spelling and grammar
    checks.append(run_spell_check(document))
    checks.append(run_grammar_check(document))

    return QAReport(checks)
```

---

## Build Configuration

### Master Build Config

```yaml
# build-config.yaml
project:
  name: "Algebra Foundations"
  version: "1.0.0"
  language: "en-US"

source:
  directory: "./sources"
  formats: ["pdf", "docx", "md"]

processing:
  ocr_enabled: true
  ocr_language: "eng"

generation:
  model_config: "./config/models.yaml"
  max_parallel_agents: 4

output:
  directory: "./output"
  formats:
    - type: "pdf"
      variant: "print"
      settings:
        color_space: "CMYK"
        resolution: 300
        bleed: 0.125
    - type: "pdf"
      variant: "digital"
      settings:
        color_space: "RGB"
        resolution: 150
        interactive: true
    - type: "epub"
      settings:
        version: 3
        responsive: true
    - type: "kindle"
      settings:
        xray: true

qa:
  spell_check: true
  grammar_check: true
  accessibility_check: true
  readability_check: true
```

### Build Commands

```bash
# Full build
textbook-builder build --config build-config.yaml

# Specific phases
textbook-builder ingest --sources ./sources
textbook-builder analyze --input ./normalized
textbook-builder generate --config ./generation-config.yaml
textbook-builder visualize --input ./draft
textbook-builder format --input ./visualized
textbook-builder export --format pdf,epub

# Quality checks
textbook-builder qa --input ./output

# Preview
textbook-builder preview --chapter 1
```

---

## Performance Optimization

### Parallel Processing

```python
# Parallelize independent operations
async def build_chapters(outline):
    tasks = []
    for chapter in outline.chapters:
        # Each chapter can be built independently
        task = asyncio.create_task(build_chapter(chapter))
        tasks.append(task)

    # But limit concurrency to avoid API rate limits
    results = await asyncio.gather(*tasks, limit=4)
    return results
```

### Caching Strategy

```python
# Cache expensive operations
@cache(ttl=3600)
def analyze_source(source_hash):
    # Cache based on content hash
    ...

@cache(ttl=86400)
def generate_image(prompt_hash, config_hash):
    # Cache generated images
    ...
```

### Incremental Builds

```python
def incremental_build(project, changes):
    # Only rebuild affected components
    affected = dependency_graph.get_affected(changes)

    for component in affected:
        rebuild(component)

    # Update only changed pages in final output
    update_output(affected)
```

---

## Error Handling

### Error Categories

```python
class BuildError(Exception):
    """Base class for build errors"""
    pass

class SourceParsingError(BuildError):
    """Failed to parse source document"""
    pass

class GenerationError(BuildError):
    """LLM generation failed"""
    pass

class QualityCheckError(BuildError):
    """Content failed quality checks"""
    pass

class FormattingError(BuildError):
    """Typesetting/layout error"""
    pass
```

### Recovery Strategies

```python
def build_with_recovery(config):
    try:
        result = build(config)
    except GenerationError as e:
        # Retry with different parameters
        result = build_with_fallback(config, e)
    except QualityCheckError as e:
        # Log issues and continue with warnings
        log_warnings(e)
        result = build_with_overrides(config)

    return result
```

---

## Monitoring & Logging

### Build Metrics

```python
@dataclass
class BuildMetrics:
    start_time: datetime
    end_time: datetime
    source_count: int
    pages_generated: int
    images_generated: int
    questions_generated: int
    tokens_used: int
    errors: List[str]
    warnings: List[str]

    def summary(self):
        return {
            'duration': self.end_time - self.start_time,
            'pages': self.pages_generated,
            'efficiency': self.pages_generated / self.tokens_used,
            'error_rate': len(self.errors) / self.pages_generated
        }
```

### Logging Configuration

```yaml
logging:
  level: INFO
  format: "%(asctime)s - %(name)s - %(levelname)s - %(message)s"

  handlers:
    - type: file
      path: "./logs/build.log"
      rotation: "daily"
    - type: console
      level: WARNING

  modules:
    ingestion: DEBUG
    generation: INFO
    formatting: INFO
```

---

*This build process documentation provides the complete technical specification for the Textbook Builder compilation pipeline.*
