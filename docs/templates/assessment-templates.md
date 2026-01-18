# Assessment Generation Templates

Comprehensive templates for generating educational assessments aligned with learning objectives and Bloom's Taxonomy.

---

## Question Generation Framework

### Question Specification Schema

```json
{
  "question": {
    "id": "q_001",
    "type": "multiple_choice|short_answer|extended_response|matching|fill_blank|true_false|calculation|diagram|essay",
    "stem": "Question text",
    "bloom_level": "remember|understand|apply|analyze|evaluate|create",
    "difficulty": "basic|intermediate|advanced",
    "learning_objective": "LO-1.2.3",
    "points": 5,
    "time_estimate": "2 minutes",
    "tags": ["topic", "subtopic"],

    "content": {
      // Type-specific content
    },

    "answer_key": {
      "correct_answer": "...",
      "explanation": "...",
      "common_errors": ["..."]
    },

    "rubric": {
      // For open-ended questions
    },

    "metadata": {
      "created": "2025-01-15",
      "reviewed": true,
      "source_reference": "Chapter 3, Section 2"
    }
  }
}
```

---

## Question Type Templates

### Multiple Choice

```json
{
  "type": "multiple_choice",
  "stem": "Which organelle is responsible for producing ATP in eukaryotic cells?",
  "options": [
    {
      "id": "A",
      "text": "Nucleus",
      "is_correct": false,
      "distractor_type": "common_misconception",
      "feedback": "The nucleus contains genetic material but does not produce ATP."
    },
    {
      "id": "B",
      "text": "Mitochondria",
      "is_correct": true,
      "feedback": "Correct! Mitochondria are the site of cellular respiration and ATP production."
    },
    {
      "id": "C",
      "text": "Ribosome",
      "is_correct": false,
      "distractor_type": "related_concept",
      "feedback": "Ribosomes are responsible for protein synthesis, not energy production."
    },
    {
      "id": "D",
      "text": "Golgi apparatus",
      "is_correct": false,
      "distractor_type": "same_category",
      "feedback": "The Golgi apparatus modifies and packages proteins."
    }
  ],
  "bloom_level": "remember",
  "difficulty": "basic"
}
```

#### Distractor Types
- **common_misconception**: Addresses frequent student errors
- **related_concept**: Similar but distinct concepts
- **partial_answer**: Incomplete or partially correct
- **same_category**: Other items from same classification
- **superficial_similarity**: Looks right but isn't

### Short Answer

```json
{
  "type": "short_answer",
  "stem": "Explain the difference between mitosis and meiosis in terms of chromosome number.",
  "bloom_level": "understand",
  "difficulty": "intermediate",
  "constraints": {
    "min_words": 20,
    "max_words": 50
  },
  "answer_key": {
    "model_answer": "Mitosis produces two daughter cells with the same chromosome number as the parent cell (diploid), while meiosis produces four daughter cells with half the chromosome number (haploid).",
    "key_concepts": [
      "mitosis = same chromosome number",
      "meiosis = half chromosome number",
      "diploid vs haploid"
    ],
    "acceptable_variations": [
      "2n to 2n for mitosis",
      "2n to n for meiosis"
    ]
  },
  "rubric": {
    "criteria": [
      {"name": "Mitosis description", "points": 2, "description": "Correctly states mitosis maintains chromosome number"},
      {"name": "Meiosis description", "points": 2, "description": "Correctly states meiosis halves chromosome number"},
      {"name": "Terminology", "points": 1, "description": "Uses diploid/haploid or 2n/n correctly"}
    ],
    "total_points": 5
  }
}
```

### Extended Response / Essay

```json
{
  "type": "extended_response",
  "stem": "Analyze the causes of the French Revolution. In your response, address economic, social, and political factors, and evaluate which was most significant.",
  "bloom_level": "evaluate",
  "difficulty": "advanced",
  "constraints": {
    "min_paragraphs": 3,
    "max_paragraphs": 5,
    "suggested_time": "25 minutes"
  },
  "scoring_guide": {
    "holistic": {
      "6": "Exceptional analysis with nuanced evaluation, strong evidence",
      "5": "Strong analysis, clear evaluation with good evidence",
      "4": "Solid analysis, reasonable evaluation, adequate evidence",
      "3": "Basic analysis, limited evaluation, some evidence",
      "2": "Weak analysis, minimal evaluation, little evidence",
      "1": "Inadequate response, major misconceptions"
    }
  },
  "rubric": {
    "dimensions": [
      {
        "name": "Economic Factors",
        "levels": {
          "4": "Thoroughly explains debt, taxation, food shortages with specific examples",
          "3": "Explains most economic factors with some examples",
          "2": "Mentions economic factors but lacks depth",
          "1": "Minimal or inaccurate economic discussion"
        }
      },
      {
        "name": "Social Factors",
        "levels": {
          "4": "Thoroughly explains Enlightenment, class tensions, with examples",
          "3": "Explains social factors with some examples",
          "2": "Mentions social factors but lacks depth",
          "1": "Minimal or inaccurate social discussion"
        }
      },
      {
        "name": "Political Factors",
        "levels": {
          "4": "Thoroughly explains monarchy, Estates General, with examples",
          "3": "Explains political factors with some examples",
          "2": "Mentions political factors but lacks depth",
          "1": "Minimal or inaccurate political discussion"
        }
      },
      {
        "name": "Evaluation/Argumentation",
        "levels": {
          "4": "Clear thesis, compelling evaluation with well-supported argument",
          "3": "Clear thesis, reasonable evaluation with support",
          "2": "Attempts evaluation but lacks clear support",
          "1": "No clear evaluation or argument"
        }
      }
    ],
    "total_points": 16
  }
}
```

### Matching

```json
{
  "type": "matching",
  "stem": "Match each scientist with their discovery or contribution.",
  "bloom_level": "remember",
  "difficulty": "basic",
  "items": [
    {"id": "1", "prompt": "Darwin", "match": "A"},
    {"id": "2", "prompt": "Mendel", "match": "B"},
    {"id": "3", "prompt": "Watson & Crick", "match": "C"},
    {"id": "4", "prompt": "Pasteur", "match": "D"}
  ],
  "options": [
    {"id": "A", "text": "Theory of Evolution by Natural Selection"},
    {"id": "B", "text": "Laws of Inheritance"},
    {"id": "C", "text": "Structure of DNA"},
    {"id": "D", "text": "Germ Theory of Disease"},
    {"id": "E", "text": "Cell Theory"}
  ],
  "answer_key": {
    "matches": {"1": "A", "2": "B", "3": "C", "4": "D"},
    "explanations": {
      "1": "Darwin developed the theory of evolution through natural selection.",
      "2": "Mendel's pea plant experiments established the laws of inheritance.",
      "3": "Watson and Crick discovered the double helix structure of DNA.",
      "4": "Pasteur's experiments led to the germ theory of disease."
    }
  }
}
```

### Fill in the Blank

```json
{
  "type": "fill_blank",
  "stem": "The process by which plants convert sunlight into chemical energy is called ___________. This process takes place primarily in the ___________.",
  "bloom_level": "remember",
  "difficulty": "basic",
  "blanks": [
    {
      "id": 1,
      "correct_answers": ["photosynthesis"],
      "acceptable_variations": ["photo-synthesis"],
      "case_sensitive": false
    },
    {
      "id": 2,
      "correct_answers": ["chloroplast", "chloroplasts"],
      "acceptable_variations": [],
      "case_sensitive": false
    }
  ],
  "word_bank": ["photosynthesis", "chloroplast", "mitochondria", "cellular respiration", "nucleus"],
  "word_bank_enabled": true
}
```

### True/False

```json
{
  "type": "true_false",
  "stem": "The Earth's inner core is liquid.",
  "bloom_level": "remember",
  "difficulty": "basic",
  "correct_answer": false,
  "explanation": "The Earth's inner core is solid due to the extreme pressure, despite the high temperature. The outer core is liquid.",
  "common_error": "Students often confuse the inner and outer core states."
}
```

### Calculation / Problem Solving

```json
{
  "type": "calculation",
  "stem": "A car travels at a constant speed of 60 km/h for 2.5 hours. How far does the car travel?",
  "bloom_level": "apply",
  "difficulty": "basic",
  "given": {
    "speed": {"value": 60, "unit": "km/h"},
    "time": {"value": 2.5, "unit": "hours"}
  },
  "solution": {
    "formula": "distance = speed × time",
    "steps": [
      {"step": 1, "action": "Identify the formula", "expression": "d = v × t"},
      {"step": 2, "action": "Substitute values", "expression": "d = 60 km/h × 2.5 h"},
      {"step": 3, "action": "Calculate", "expression": "d = 150 km"}
    ],
    "answer": {"value": 150, "unit": "km"},
    "significant_figures": 2
  },
  "rubric": {
    "criteria": [
      {"name": "Correct formula", "points": 1},
      {"name": "Correct substitution", "points": 1},
      {"name": "Correct calculation", "points": 1},
      {"name": "Correct units", "points": 1}
    ],
    "total_points": 4
  }
}
```

### Diagram/Labeling

```json
{
  "type": "diagram",
  "stem": "Label the parts of the cell indicated by the arrows.",
  "bloom_level": "remember",
  "difficulty": "basic",
  "image_reference": "figures/cell_diagram_unlabeled.png",
  "labels": [
    {"id": 1, "position": {"x": 120, "y": 85}, "correct_answer": "nucleus"},
    {"id": 2, "position": {"x": 200, "y": 150}, "correct_answer": "mitochondria"},
    {"id": 3, "position": {"x": 80, "y": 200}, "correct_answer": "cell membrane"},
    {"id": 4, "position": {"x": 160, "y": 180}, "correct_answer": "cytoplasm"}
  ],
  "word_bank": ["nucleus", "mitochondria", "cell membrane", "cytoplasm", "ribosome", "vacuole"]
}
```

---

## Bloom's Level Question Stems

### Remember (Knowledge)
- Define...
- List...
- Identify...
- Name...
- What is...?
- When did...?
- Where is...?
- Who...?
- Which...?

### Understand (Comprehension)
- Explain...
- Describe...
- Summarize...
- Paraphrase...
- What is the main idea of...?
- Compare...
- Contrast...
- Interpret...

### Apply (Application)
- How would you use...?
- Solve...
- Calculate...
- Demonstrate...
- Apply...to...
- What would happen if...?
- How would you show...?

### Analyze (Analysis)
- Why did...?
- What are the causes of...?
- What evidence supports...?
- How does...relate to...?
- Differentiate between...
- What conclusions can you draw...?
- Categorize...

### Evaluate (Evaluation)
- Do you agree that...? Why?
- What is your opinion of...?
- Justify...
- Critique...
- Assess the value of...
- Which solution is best? Why?
- Rate...according to...

### Create (Synthesis)
- Design...
- Create...
- Develop...
- Propose...
- What if...?
- How would you improve...?
- Construct...

---

## Assessment Types by Purpose

### Formative Assessments

#### Quick Checks (1-2 minutes)
```yaml
purpose: Check understanding during lesson
frequency: Every 10-15 minutes
format:
  - 1-2 multiple choice
  - Thumbs up/down
  - Exit ticket
feedback: Immediate
stakes: None (not graded)
```

#### Practice Sets (10-15 minutes)
```yaml
purpose: Reinforce new skills
frequency: After each lesson
format:
  - 5-10 problems
  - Scaffolded difficulty
  - Worked example included
feedback: Self-check with answer key
stakes: Low (completion grade)
```

### Summative Assessments

#### Chapter Test (30-45 minutes)
```yaml
purpose: Evaluate chapter mastery
frequency: End of each chapter
structure:
  - Part A: Multiple choice (40%)
  - Part B: Short answer (30%)
  - Part C: Extended response (30%)
difficulty_distribution:
  basic: 30%
  intermediate: 50%
  advanced: 20%
bloom_distribution:
  remember: 20%
  understand: 30%
  apply: 30%
  analyze: 15%
  evaluate: 5%
```

#### Unit Exam (60-90 minutes)
```yaml
purpose: Comprehensive unit evaluation
frequency: End of unit (3-4 chapters)
structure:
  - Part A: Multiple choice (35%)
  - Part B: Short answer (25%)
  - Part C: Problems/calculations (25%)
  - Part D: Extended response (15%)
includes:
  - Cumulative content
  - Cross-chapter connections
  - Application scenarios
```

---

## Difficulty Calibration

### Factors Affecting Difficulty

| Factor | Basic | Intermediate | Advanced |
|--------|-------|--------------|----------|
| Steps | 1-2 | 3-4 | 5+ |
| Context | Familiar | Moderate transfer | Novel |
| Information | Given directly | Some inference | Significant inference |
| Scaffolding | Heavy | Moderate | Minimal |
| Time pressure | Generous | Standard | Tight |

### Difficulty Distribution Guidelines

| Assessment Type | Basic | Intermediate | Advanced |
|----------------|-------|--------------|----------|
| Practice set | 50% | 40% | 10% |
| Chapter test | 30% | 50% | 20% |
| Unit exam | 25% | 50% | 25% |
| Final exam | 20% | 50% | 30% |

---

## Answer Key Format

### Complete Answer Key Template

```json
{
  "assessment_id": "ch3_test",
  "answer_key": {
    "version": "1.0",
    "questions": [
      {
        "question_id": "q1",
        "correct_answer": "B",
        "explanation": "The mitochondria is known as the powerhouse of the cell because it produces ATP through cellular respiration.",
        "common_errors": [
          {"answer": "A", "error": "Confuses nucleus with energy production"},
          {"answer": "C", "error": "Confuses ribosomes with mitochondria"}
        ],
        "teaching_notes": "Review cellular organelles diagram"
      }
    ]
  }
}
```

---

## Quality Assurance Checklist

### Question Quality Criteria

- [ ] Aligned with learning objective
- [ ] Appropriate Bloom's level
- [ ] Clear, unambiguous wording
- [ ] Single correct answer (for MC)
- [ ] Plausible distractors
- [ ] No clues in stem
- [ ] Appropriate difficulty
- [ ] Reviewed for bias
- [ ] Accessible language
- [ ] Tested on sample students

### Assessment Balance Criteria

- [ ] Covers all learning objectives
- [ ] Appropriate difficulty distribution
- [ ] Appropriate Bloom's distribution
- [ ] Reasonable time allocation
- [ ] Clear instructions
- [ ] Consistent formatting
- [ ] Answer key complete
- [ ] Rubrics clear and complete

---

*Use these templates to generate high-quality, aligned assessments for any educational content.*
