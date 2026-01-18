# Robust Formatting Pipeline

A comprehensive system to overcome LLM formatting weaknesses through structured content, automated typesetting, and deterministic output generation.

---

## The LLM Formatting Problem

### Known Weaknesses

LLMs struggle with:
- **Precise spacing**: Inconsistent margins, indentation, line breaks
- **Complex layouts**: Multi-column, text wrapping around figures
- **Mathematical typesetting**: Equation alignment, numbering
- **Consistent styling**: Headers, fonts, colors across documents
- **Page composition**: Avoiding widows/orphans, managing breaks
- **Cross-references**: Figure numbers, page references
- **Tables**: Complex layouts, cell spanning, borders

### Our Solution: Separation of Concerns

```
┌────────────────────────────────────────────────────────────┐
│                    LLM GENERATES                            │
│           Structured Content (What to say)                  │
│                                                             │
│   • Semantic markup (XML/JSON)                              │
│   • Content with embedded metadata                          │
│   • Explicit structure tags                                 │
│   • NO formatting decisions                                 │
└───────────────────────┬────────────────────────────────────┘
                        │
                        ▼
┌────────────────────────────────────────────────────────────┐
│                 DETERMINISTIC SYSTEM                        │
│            Applies Formatting (How it looks)                │
│                                                             │
│   • Template-based typesetting                              │
│   • Rule-based layout engine                                │
│   • Automated composition                                   │
│   • Programmatic styling                                    │
└────────────────────────────────────────────────────────────┘
```

**Key Principle**: LLMs write content in structured format; deterministic systems render it.

---

## Structured Content Format

### Content Intermediate Representation (CIR)

LLMs generate content in a semantic XML format that separates content from presentation:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<lesson id="lesson_001" version="1.0">
  <metadata>
    <title>Understanding Slope</title>
    <domain>mathematics</domain>
    <grade-band>4</grade-band>
    <duration-minutes>50</duration-minutes>
    <primary-skill>proportional_reasoning</primary-skill>
  </metadata>

  <learning-objectives>
    <objective id="obj_1" bloom="understand">
      Explain the meaning of slope as rate of change
    </objective>
    <objective id="obj_2" bloom="apply">
      Calculate slope from two points on a line
    </objective>
  </learning-objectives>

  <content>
    <section type="introduction">
      <hook>
        <para>
          Have you ever wondered why some hills are harder to climb than others?
          The answer lies in a mathematical concept called <term>slope</term>.
        </para>
      </hook>

      <para>
        Slope measures the <emphasis>steepness</emphasis> of a line. In this
        lesson, we'll learn how to calculate and interpret slope.
      </para>
    </section>

    <section type="main-content">
      <subsection title="What is Slope?">
        <definition term="slope">
          <formal>
            The ratio of vertical change to horizontal change between any
            two points on a line.
          </formal>
          <formula notation="latex">
            m = \frac{\Delta y}{\Delta x} = \frac{y_2 - y_1}{x_2 - x_1}
          </formula>
        </definition>

        <visual-placeholder type="diagram" id="slope_triangle">
          <description>
            Coordinate plane showing a line with two points marked.
            A right triangle formed showing rise and run.
            Labels: rise (vertical), run (horizontal), slope = rise/run.
          </description>
          <required-elements>
            <element>coordinate_grid</element>
            <element>line_segment</element>
            <element>slope_triangle</element>
            <element>labels</element>
          </required-elements>
        </visual-placeholder>

        <para>
          The slope tells us how much <math>y</math> changes for every
          unit change in <math>x</math>.
        </para>
      </subsection>

      <subsection title="Worked Example">
        <example id="ex_1">
          <problem>
            Find the slope of the line passing through points
            <math>(2, 3)</math> and <math>(5, 9)</math>.
          </problem>
          <solution>
            <step number="1">
              <action>Identify the coordinates</action>
              <work>
                <math>(x_1, y_1) = (2, 3)</math> and
                <math>(x_2, y_2) = (5, 9)</math>
              </work>
            </step>
            <step number="2">
              <action>Apply the slope formula</action>
              <work>
                <formula notation="latex">
                  m = \frac{9 - 3}{5 - 2} = \frac{6}{3} = 2
                </formula>
              </work>
            </step>
            <step number="3">
              <action>Interpret the result</action>
              <explanation>
                A slope of 2 means the line rises 2 units for every
                1 unit of horizontal movement.
              </explanation>
            </step>
          </solution>
          <answer>The slope is <math>m = 2</math></answer>
        </example>
      </subsection>
    </section>

    <section type="practice">
      <exercise-set>
        <exercise id="ex_p1" difficulty="basic" bloom="apply">
          <problem>
            Find the slope of the line through <math>(1, 4)</math>
            and <math>(3, 10)</math>.
          </problem>
          <answer>3</answer>
          <solution-key>
            <math>m = (10-4)/(3-1) = 6/2 = 3</math>
          </solution-key>
        </exercise>
      </exercise-set>
    </section>
  </content>

  <cross-curricular>
    <connection domain="science" concept="velocity">
      Slope represents rate of change, just like velocity in physics
      (change in position over change in time).
    </connection>
  </cross-curricular>
</lesson>
```

---

## Typesetting Engine Architecture

### Multi-Backend Support

```
┌────────────────────────────────────────────────────────────┐
│                    CIR (Structured Content)                 │
└───────────────────────┬────────────────────────────────────┘
                        │
            ┌───────────┴───────────┐
            │   Backend Selector    │
            └───────────┬───────────┘
                        │
        ┌───────────────┼───────────────┐
        │               │               │
        ▼               ▼               ▼
  ┌───────────┐   ┌───────────┐   ┌───────────┐
  │   LaTeX   │   │  Pandoc/  │   │  Typefi/  │
  │  Backend  │   │  Markdown │   │  InDesign │
  └─────┬─────┘   └─────┬─────┘   └─────┬─────┘
        │               │               │
        ▼               ▼               ▼
  ┌───────────┐   ┌───────────┐   ┌───────────┐
  │    PDF    │   │   EPUB    │   │  Print-   │
  │   (Math)  │   │  (Digital)│   │   Ready   │
  └───────────┘   └───────────┘   └───────────┘
```

### Backend Selection Logic

```python
def select_backend(content, output_format, requirements):
    """Select optimal typesetting backend."""

    if output_format == "pdf":
        if requirements.heavy_math:
            return LaTeXBackend()  # Best for math
        elif requirements.complex_layout:
            return TypefiBackend()  # Best for complex layouts
        else:
            return PandocBackend()  # Good general purpose

    elif output_format == "epub":
        return PandocBackend()  # Good for reflowable

    elif output_format == "print":
        return TypefiBackend()  # Professional print

    else:
        return PandocBackend()  # Default
```

---

## LaTeX Pipeline (Mathematics-Heavy Content)

### Template System

```latex
% master-template.tex
\documentclass[11pt,twoside]{book}

% Typography settings
\usepackage[T1]{fontenc}
\usepackage{mathpazo}  % Palatino for text and math
\usepackage{microtype}  % Better typography

% Page layout
\usepackage[
  papersize={6in,9in},
  inner=0.75in,
  outer=0.5in,
  top=0.75in,
  bottom=0.75in
]{geometry}

% Math packages
\usepackage{amsmath,amssymb,amsthm}
\usepackage{mathtools}

% Custom environments (automatically applied)
\newtheorem{definition}{Definition}[chapter]
\newtheorem{example}{Example}[chapter]

% Cross-references
\usepackage{hyperref}
\usepackage{cleveref}

% Include generated content
\input{generated-content.tex}
```

### CIR to LaTeX Transformer

```python
class CIRToLaTeX:
    def transform(self, cir_document):
        latex = []

        for element in cir_document.elements:
            if element.type == "section":
                latex.append(self.render_section(element))
            elif element.type == "definition":
                latex.append(self.render_definition(element))
            elif element.type == "example":
                latex.append(self.render_example(element))
            elif element.type == "formula":
                latex.append(self.render_formula(element))
            elif element.type == "exercise":
                latex.append(self.render_exercise(element))

        return "\n".join(latex)

    def render_definition(self, element):
        return f"""
\\begin{{definition}}[{element.term}]
{element.formal}

{self.render_formula(element.formula)}
\\end{{definition}}
"""

    def render_example(self, element):
        steps = "\n".join([
            f"\\textbf{{Step {s.number}:}} {s.action}\n\n{s.work}"
            for s in element.steps
        ])
        return f"""
\\begin{{example}}
\\textbf{{Problem:}} {element.problem}

\\textbf{{Solution:}}
{steps}

\\textbf{{Answer:}} {element.answer}
\\end{{example}}
"""
```

---

## Pandoc Pipeline (General Content)

### Markdown Intermediate Format

```markdown
---
title: "Understanding Slope"
author: "Textbook Builder"
documentclass: book
geometry: "margin=1in"
---

# Understanding Slope

## Learning Objectives

By the end of this lesson, you will be able to:

1. Explain the meaning of slope as rate of change
2. Calculate slope from two points on a line

## Introduction

Have you ever wondered why some hills are harder to climb than others?
The answer lies in a mathematical concept called **slope**.

Slope measures the *steepness* of a line. In this lesson, we'll learn
how to calculate and interpret slope.

## What is Slope?

::: {.definition}
**Slope**: The ratio of vertical change to horizontal change between
any two points on a line.

$$m = \frac{\Delta y}{\Delta x} = \frac{y_2 - y_1}{x_2 - x_1}$$
:::

![Slope Triangle Diagram](assets/slope_triangle.png){#fig:slope-triangle}

The slope tells us how much $y$ changes for every unit change in $x$.
```

### Pandoc Build Command

```bash
pandoc content.md \
  --from markdown \
  --to pdf \
  --pdf-engine=xelatex \
  --template=textbook-template.tex \
  --toc \
  --number-sections \
  --filter pandoc-crossref \
  --citeproc \
  --output output.pdf
```

---

## Typefi Pipeline (Professional Print)

### InDesign Server Integration

```python
class TypefiBackend:
    def __init__(self, api_key):
        self.client = TypefiClient(api_key)

    def render(self, cir_document, template_id):
        # Convert CIR to Typefi XML
        typefi_xml = self.cir_to_typefi(cir_document)

        # Upload to Typefi
        job_id = self.client.create_job(
            template_id=template_id,
            content=typefi_xml,
            output_format="pdf"
        )

        # Wait for completion
        result = self.client.wait_for_job(job_id)

        return result.download_url

    def cir_to_typefi(self, cir):
        """Convert CIR to Typefi-compatible XML."""
        # Typefi uses specific tag conventions
        root = ET.Element("document")

        for element in cir.elements:
            typefi_element = self.map_element(element)
            root.append(typefi_element)

        return ET.tostring(root, encoding="unicode")
```

---

## Page Composition Engine

### Handling Complex Layouts

The system uses a rule-based page composition engine to handle layouts that LLMs cannot:

```python
class PageComposer:
    def __init__(self, page_spec):
        self.page_width = page_spec.width
        self.page_height = page_spec.height
        self.margins = page_spec.margins

    def compose_pages(self, content_elements):
        pages = []
        current_page = Page(self.page_spec)

        for element in content_elements:
            # Check if element fits
            if not current_page.can_fit(element):
                # Handle page break
                self.handle_page_break(current_page, element)
                pages.append(current_page)
                current_page = Page(self.page_spec)

            # Place element
            self.place_element(current_page, element)

        return pages

    def handle_page_break(self, current_page, next_element):
        # Prevent widows (single line at top of page)
        if next_element.type == "paragraph":
            if next_element.line_count > 1:
                # Move last line of current para to new page
                self.break_paragraph(current_page.last_paragraph)

        # Prevent orphans (single line at bottom of page)
        if current_page.remaining_space < self.line_height * 2:
            # Move paragraph start to new page
            current_page.remove_last_paragraph()

        # Keep headers with content
        if next_element.type == "header":
            # Ensure at least 2 lines follow header
            if current_page.remaining_space < self.header_keep_space:
                current_page.add_page_break()

    def place_float(self, page, float_element):
        """Place floating elements (figures, tables)."""
        # Try preferred position
        positions = ["top", "bottom", "inline"]

        for pos in float_element.preferences:
            if page.can_place_at(float_element, pos):
                page.place_at(float_element, pos)
                return

        # Defer to next page if necessary
        self.defer_float(float_element)
```

### Widow/Orphan Prevention

```python
def prevent_widows_orphans(document):
    """Post-process to eliminate widows and orphans."""
    for i, page in enumerate(document.pages):
        # Check first line of page
        first_element = page.elements[0]
        if (first_element.type == "paragraph" and
            first_element.is_continuation and
            first_element.lines_on_page == 1):
            # Widow detected: pull back previous line
            pull_line_from_previous_page(document, i)

        # Check last lines of page
        last_element = page.elements[-1]
        if (last_element.type == "paragraph" and
            last_element.continues_to_next and
            page.lines_remaining(last_element) == 1):
            # Orphan detected: push to next page
            push_paragraph_to_next_page(document, i)
```

---

## Style Sheet System

### Declarative Style Definitions

```yaml
# style-sheets/high-school-math.yaml
document:
  page:
    size: "6x9"
    margins:
      top: 0.75in
      bottom: 0.75in
      inner: 0.75in
      outer: 0.5in

typography:
  body:
    font: "Georgia"
    size: 11pt
    line_height: 1.35
    paragraph_spacing: 6pt
    first_line_indent: 0.25in

  headings:
    h1:
      font: "Helvetica Bold"
      size: 18pt
      color: "#1a5276"
      spacing_before: 24pt
      spacing_after: 12pt
    h2:
      font: "Helvetica Bold"
      size: 14pt
      color: "#2874a6"
      spacing_before: 18pt
      spacing_after: 6pt
    h3:
      font: "Helvetica"
      size: 12pt
      color: "#333333"
      style: "italic"

  math:
    font: "Palatino Math"
    inline_size: 11pt
    display_size: 12pt

elements:
  definition:
    background: "#e8f4f8"
    border_left: "3px solid #3498db"
    padding: 12pt
    margin: 12pt 0

  example:
    border: "1px solid #cccccc"
    padding: 12pt
    margin: 12pt 0

  exercise:
    numbering: "chapter.exercise"
    margin_left: 0.5in

  figure:
    caption_position: "below"
    caption_font: "Georgia Italic"
    caption_size: 10pt
    alignment: "center"

  table:
    header_background: "#f0f0f0"
    border: "1px solid #cccccc"
    cell_padding: 6pt
```

### Style Application

```python
class StyleApplicator:
    def __init__(self, style_sheet):
        self.styles = yaml.load(style_sheet)

    def apply_styles(self, document):
        for element in document.walk():
            element_type = element.type
            if element_type in self.styles['elements']:
                self.apply_element_style(
                    element,
                    self.styles['elements'][element_type]
                )

    def apply_element_style(self, element, style):
        for property, value in style.items():
            element.set_style(property, value)
```

---

## Mathematical Equation Handling

### Equation Numbering System

```python
class EquationNumbering:
    def __init__(self, scheme="chapter.equation"):
        self.scheme = scheme
        self.counters = {}

    def number_equations(self, document):
        for chapter in document.chapters:
            self.counters['equation'] = 0

            for equation in chapter.find_all("equation", numbered=True):
                self.counters['equation'] += 1
                equation.number = f"{chapter.number}.{self.counters['equation']}"
```

### Equation Alignment

```latex
% Automatic alignment for multi-step solutions
\begin{align}
  3x + 5 &= 14 \\
  3x &= 14 - 5 \\
  3x &= 9 \\
  x &= 3
\end{align}
```

### Inline vs Display Math

```python
def format_math(math_element):
    if math_element.context == "inline":
        return f"${math_element.latex}$"
    else:
        if math_element.numbered:
            return f"""
\\begin{{equation}}
{math_element.latex}
\\label{{{math_element.id}}}
\\end{{equation}}
"""
        else:
            return f"""
\\[
{math_element.latex}
\\]
"""
```

---

## Cross-Reference System

### Automatic Reference Resolution

```python
class CrossReferenceResolver:
    def __init__(self, document):
        self.document = document
        self.build_reference_index()

    def build_reference_index(self):
        self.index = {}
        for element in self.document.walk():
            if element.id:
                self.index[element.id] = {
                    'type': element.type,
                    'number': element.number,
                    'page': element.page,
                    'title': element.title
                }

    def resolve(self, ref_id, format="full"):
        ref = self.index.get(ref_id)
        if not ref:
            raise ReferenceError(f"Unknown reference: {ref_id}")

        if format == "full":
            return f"{ref['type'].title()} {ref['number']}"
        elif format == "number":
            return ref['number']
        elif format == "page":
            return str(ref['page'])
        elif format == "title":
            return ref['title']
```

### Reference Formatting

```xml
<!-- In CIR -->
<reference target="fig:slope-triangle" format="full"/>

<!-- Rendered as -->
Figure 3.1
```

---

## Quality Assurance Pipeline

### Automated Checks

```python
class FormattingQA:
    def run_checks(self, document):
        checks = [
            self.check_font_consistency,
            self.check_spacing_consistency,
            self.check_widow_orphans,
            self.check_hyphenation,
            self.check_figure_placement,
            self.check_cross_references,
            self.check_page_numbers,
            self.check_toc_accuracy,
            self.check_index_entries,
        ]

        results = []
        for check in checks:
            result = check(document)
            results.append(result)

        return QAReport(results)

    def check_font_consistency(self, document):
        """Verify all fonts match style sheet."""
        issues = []
        expected_fonts = self.style_sheet.get_fonts()

        for element in document.walk():
            if element.font not in expected_fonts:
                issues.append(
                    f"Unexpected font '{element.font}' at {element.location}"
                )

        return CheckResult("Font Consistency", issues)

    def check_widow_orphans(self, document):
        """Detect remaining widows and orphans."""
        issues = []

        for page in document.pages:
            # Check for widows
            first = page.first_element()
            if (first.type == "paragraph" and
                first.is_continuation and
                first.line_count == 1):
                issues.append(f"Widow on page {page.number}")

            # Check for orphans
            last = page.last_element()
            if (last.type == "paragraph" and
                last.continues and
                page.lines_of(last) == 1):
                issues.append(f"Orphan on page {page.number}")

        return CheckResult("Widow/Orphan", issues)
```

---

## Output Format Matrix

| Feature | LaTeX | Pandoc | Typefi |
|---------|-------|--------|--------|
| Math rendering | Excellent | Good | Good |
| Complex layouts | Limited | Limited | Excellent |
| Automation | High | High | High |
| Cost | Free | Free | Paid |
| Setup complexity | Medium | Low | High |
| Print quality | Excellent | Good | Excellent |
| EPUB output | Limited | Excellent | Good |

---

## Build Configuration

```yaml
# formatting-config.yaml
formatting:
  backend:
    default: "pandoc"
    math_heavy: "latex"
    print_production: "typefi"

  style_sheet: "styles/high-school-math.yaml"

  latex:
    engine: "xelatex"
    template: "templates/textbook.tex"
    packages:
      - "amsmath"
      - "amssymb"
      - "graphicx"
      - "hyperref"

  pandoc:
    template: "templates/textbook-pandoc.tex"
    filters:
      - "pandoc-crossref"
      - "pandoc-citeproc"
    pdf_engine: "xelatex"

  typefi:
    api_endpoint: "https://api.typefi.com"
    template_id: "textbook-standard"

  qa:
    enabled: true
    fail_on: ["font_consistency", "cross_references"]
    warn_on: ["widow_orphan", "hyphenation"]
```

---

*The Formatting Pipeline ensures that LLM-generated content is transformed into professionally typeset documents through deterministic, rule-based systems that compensate for LLM formatting weaknesses.*
