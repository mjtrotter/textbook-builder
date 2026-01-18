# Cross-Subject Knowledge Graph Architecture

A unified knowledge representation that interconnects concepts across all subjects and levels, enabling seamless cross-curricular integration.

---

## Overview

The Knowledge Graph (KG) serves as the backbone of the unified learning ecosystem. It represents:
- **Concepts** (what students learn)
- **Skills** (how students think)
- **Prerequisites** (what must come first)
- **Cross-curricular connections** (how knowledge transfers)
- **Learning progressions** (how understanding develops)

---

## Graph Structure

### Node Types

```
┌─────────────────────────────────────────────────────────────┐
│                     NODE TYPES                               │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌─────────┐   ┌─────────┐   ┌─────────┐   ┌─────────┐      │
│  │ CONCEPT │   │  SKILL  │   │  TOPIC  │   │ LESSON  │      │
│  └─────────┘   └─────────┘   └─────────┘   └─────────┘      │
│                                                              │
│  ┌─────────┐   ┌─────────┐   ┌─────────┐   ┌─────────┐      │
│  │ DOMAIN  │   │  BAND   │   │ STANDARD│   │  ASSET  │      │
│  └─────────┘   └─────────┘   └─────────┘   └─────────┘      │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

#### 1. Concept Node
The atomic unit of knowledge.

```json
{
  "id": "concept:fractions_basic",
  "type": "concept",
  "name": "Basic Fractions",
  "description": "Understanding fractions as parts of a whole",
  "domains": ["mathematics"],
  "aliases": ["simple fractions", "elementary fractions"],
  "difficulty": 0.3,
  "abstractness": 0.4,
  "properties": {
    "visual_representation": true,
    "requires_manipulation": true
  }
}
```

#### 2. Skill Node
Transferable cognitive skill.

```json
{
  "id": "skill:proportional_reasoning",
  "type": "skill",
  "name": "Proportional Reasoning",
  "category": "reasoning",
  "description": "Ability to reason about multiplicative relationships",
  "progression_levels": 6,
  "cross_domain": true,
  "properties": {
    "cognitive_load": "high",
    "transfer_potential": "very_high"
  }
}
```

#### 3. Topic Node
A collection of related concepts forming a unit of study.

```json
{
  "id": "topic:linear_functions",
  "type": "topic",
  "name": "Linear Functions",
  "domain": "mathematics",
  "grade_band": 4,
  "concepts": [
    "concept:slope",
    "concept:y_intercept",
    "concept:rate_of_change"
  ],
  "estimated_hours": 12
}
```

#### 4. Lesson Node
A specific instructional unit.

```json
{
  "id": "lesson:slope_intro",
  "type": "lesson",
  "topic": "topic:linear_functions",
  "sequence": 1,
  "title": "Understanding Slope",
  "duration_minutes": 50,
  "assets": ["asset:slope_diagram_001", "asset:slope_calculator"]
}
```

---

### Edge Types (Relationships)

#### Prerequisite Relationships

```
concept:A --[REQUIRES]--> concept:B
```
Concept A requires understanding of concept B first.

**Types**:
- `HARD_PREREQUISITE`: Absolutely required
- `SOFT_PREREQUISITE`: Strongly recommended
- `HELPFUL_BACKGROUND`: Useful but not essential

```json
{
  "from": "concept:quadratic_equations",
  "to": "concept:linear_equations",
  "type": "HARD_PREREQUISITE",
  "strength": 0.95,
  "rationale": "Quadratic solving methods build on linear equation techniques"
}
```

#### Cross-Curricular Connections

```
concept:A --[ANALOGOUS_TO]--> concept:B
```
Same underlying structure in different domains.

**Types**:
- `ANALOGOUS_TO`: Same pattern/structure
- `APPLIED_IN`: Used in another domain
- `SHARES_SKILL`: Same skill expressed differently

```json
{
  "from": "concept:balancing_equations_chemistry",
  "to": "concept:solving_equations_algebra",
  "type": "ANALOGOUS_TO",
  "shared_skill": "skill:algebraic_reasoning",
  "connection_explanation": "Both require isolating variables and maintaining equality"
}
```

#### Skill Development

```
lesson:A --[DEVELOPS]--> skill:B
```
Lesson develops a particular skill.

```json
{
  "from": "lesson:slope_intro",
  "to": "skill:proportional_reasoning",
  "type": "DEVELOPS",
  "level": 4,
  "intensity": "primary"  // primary, secondary, incidental
}
```

#### Progression Relationships

```
concept:A --[PROGRESSES_TO]--> concept:B
```
Learning progression from simpler to more complex.

```json
{
  "from": "concept:fractions_basic",
  "to": "concept:fractions_operations",
  "type": "PROGRESSES_TO",
  "typical_gap": "6_months",
  "same_concept_family": true
}
```

---

## Graph Schema

### Complete Ontology

```
NODES:
  Concept
    - id: string (unique)
    - name: string
    - description: text
    - domains: [string]
    - difficulty: float [0-1]
    - abstractness: float [0-1]
    - grade_bands: [int]

  Skill
    - id: string (unique)
    - name: string
    - category: enum (reasoning, visualization, communication, analysis, problem_solving, collaboration, metacognition)
    - levels: int
    - description: text

  Topic
    - id: string (unique)
    - name: string
    - domain: string
    - grade_band: int
    - concepts: [concept_id]
    - hours: float

  Lesson
    - id: string (unique)
    - topic_id: string
    - title: string
    - sequence: int
    - duration: int
    - assets: [asset_id]

  Domain
    - id: string (unique)
    - name: string
    - description: text
    - subdomains: [domain_id]

  Asset
    - id: string (unique)
    - type: enum (image, diagram, video, interactive)
    - path: string
    - concepts: [concept_id]
    - reusable: boolean

EDGES:
  REQUIRES (Concept → Concept)
    - type: enum (hard, soft, helpful)
    - strength: float [0-1]

  ANALOGOUS_TO (Concept → Concept)
    - shared_skill: skill_id
    - explanation: text

  APPLIED_IN (Concept → Concept)
    - context: string

  DEVELOPS (Lesson → Skill)
    - level: int
    - intensity: enum (primary, secondary, incidental)

  PROGRESSES_TO (Concept → Concept)
    - typical_gap: string
    - same_family: boolean

  BELONGS_TO (Concept → Domain)

  VISUALIZES (Asset → Concept)
    - adaptation_potential: float
```

---

## Cross-Curricular Connection Types

### 1. Structural Analogies
Same underlying pattern in different contexts.

**Example: Proportional Relationships**
```
Math: y = kx (direct variation)
Science: F = ma (force equation)
Economics: Revenue = Price × Quantity
Music: Frequency ratios in harmony
```

**Graph Representation**:
```
concept:direct_variation --[ANALOGOUS_TO {skill: proportional_reasoning}]--> concept:force_equation
concept:direct_variation --[ANALOGOUS_TO {skill: proportional_reasoning}]--> concept:revenue_calculation
concept:direct_variation --[ANALOGOUS_TO {skill: proportional_reasoning}]--> concept:frequency_ratios
```

### 2. Methodological Transfers
Same method applied in different domains.

**Example: Hypothesis Testing**
```
Science: Design experiment, collect data, analyze
History: Form thesis, gather evidence, evaluate
Literature: Propose interpretation, find textual evidence, argue
Math: Conjecture, test cases, prove or disprove
```

### 3. Conceptual Isomorphisms
Deep structural similarity requiring abstraction.

**Example: Exponential Growth**
```
Math: f(x) = a·bˣ
Biology: Population growth
Finance: Compound interest
Physics: Radioactive decay
Epidemiology: Disease spread
```

### 4. Skill Expressions
Same skill manifested in domain-specific ways.

**Example: Pattern Recognition**
```
Math: Sequence patterns, function behavior
Science: Data patterns, periodic phenomena
History: Historical cycles, causal patterns
Language: Literary patterns, grammatical patterns
Music: Rhythmic patterns, melodic patterns
```

---

## Prerequisite Network

### Dependency Resolution

When generating content, the system must resolve all prerequisites:

```python
def get_learning_path(target_concept, student_knowledge):
    """Generate optimal learning path to target concept."""
    # Get all prerequisites (transitive closure)
    all_prereqs = graph.get_transitive_closure(
        target_concept,
        edge_type="REQUIRES"
    )

    # Remove already-known concepts
    needed = all_prereqs - student_knowledge

    # Topological sort by dependency
    ordered = topological_sort(needed)

    # Estimate time
    time_estimate = sum(c.estimated_hours for c in ordered)

    return LearningPath(
        concepts=ordered,
        target=target_concept,
        estimated_hours=time_estimate
    )
```

### Prerequisite Strength Calculation

```python
def calculate_prerequisite_strength(concept_a, concept_b):
    """Calculate how strongly B is prerequisite for A."""
    # Direct edge strength
    direct = graph.get_edge(concept_a, concept_b, "REQUIRES")
    if direct:
        return direct.strength

    # Indirect (through shared skills)
    shared_skills = get_shared_skills(concept_a, concept_b)
    skill_overlap = len(shared_skills) / max(
        len(concept_a.skills),
        len(concept_b.skills)
    )

    # Difficulty progression
    difficulty_gap = concept_a.difficulty - concept_b.difficulty
    if difficulty_gap < 0:
        return 0  # B is harder than A

    return skill_overlap * (1 - math.exp(-difficulty_gap))
```

---

## Cross-Curricular Query Examples

### Find Analogous Concepts
```cypher
// Find all concepts analogous to "balancing equations" in chemistry
MATCH (chem:Concept {name: "Balancing Chemical Equations"})
      -[:ANALOGOUS_TO]->(related:Concept)
RETURN related.name, related.domain
```

### Find Learning Path
```cypher
// Find all prerequisites for "Calculus I"
MATCH path = (calc:Concept {name: "Calculus I"})
              -[:REQUIRES*1..10]->(prereq:Concept)
RETURN nodes(path)
ORDER BY length(path) DESC
```

### Find Skill Connections
```cypher
// Find all lessons across domains that develop "proportional reasoning"
MATCH (lesson:Lesson)-[:DEVELOPS {intensity: "primary"}]->
      (skill:Skill {name: "Proportional Reasoning"})
RETURN lesson.title, lesson.domain, lesson.grade_band
ORDER BY lesson.grade_band
```

### Find Reusable Visualizations
```cypher
// Find all assets that visualize "rate of change" concept
MATCH (asset:Asset)-[:VISUALIZES]->(concept:Concept {name: "Rate of Change"})
WHERE asset.reusable = true
RETURN asset.id, asset.type, asset.path
```

---

## Graph Population Pipeline

### Phase 1: Seed with Standards
```python
def populate_from_standards(standards_file):
    """Populate graph from curriculum standards."""
    standards = parse_standards(standards_file)

    for standard in standards:
        # Create concept node
        concept = create_concept_node(standard)

        # Extract implied prerequisites
        prereqs = extract_prerequisites(standard.description)
        for prereq in prereqs:
            create_edge(concept, prereq, "REQUIRES")

        # Map to skills
        skills = map_to_skills(standard)
        for skill in skills:
            create_edge(concept, skill, "DEVELOPS")
```

### Phase 2: Extract from Sources
```python
def extract_concepts_from_text(text, domain):
    """Extract concepts and relationships from source text."""
    # Named entity recognition for domain concepts
    concepts = ner_model.extract(text, domain=domain)

    # Relationship extraction
    for concept in concepts:
        # Find prerequisite relationships
        prereqs = extract_prerequisite_relations(text, concept)
        for prereq in prereqs:
            create_edge(concept, prereq, "REQUIRES")

        # Find analogies
        analogies = find_analogies(concept, all_concepts)
        for analogy in analogies:
            create_edge(concept, analogy, "ANALOGOUS_TO")
```

### Phase 3: Cross-Curricular Enrichment
```python
def enrich_cross_curricular():
    """Find and create cross-curricular connections."""
    all_concepts = graph.get_all_concepts()

    for concept_a in all_concepts:
        for concept_b in all_concepts:
            if concept_a.domain != concept_b.domain:
                # Check for structural similarity
                similarity = calculate_structural_similarity(
                    concept_a, concept_b
                )
                if similarity > THRESHOLD:
                    create_edge(
                        concept_a,
                        concept_b,
                        "ANALOGOUS_TO",
                        strength=similarity
                    )
```

---

## Learning Path Generation

### Personalized Paths
```python
def generate_personalized_path(
    student_profile,
    target_concept,
    preferences
):
    """Generate personalized learning path."""
    # Get base path
    base_path = get_learning_path(
        target_concept,
        student_profile.known_concepts
    )

    # Optimize for preferences
    if preferences.cross_curricular:
        # Add cross-curricular connections
        path = enrich_with_analogies(base_path)

    if preferences.skill_focus:
        # Prioritize lessons developing target skills
        path = prioritize_skill_development(
            path,
            preferences.skill_focus
        )

    if preferences.learning_style == "visual":
        # Prioritize visually-rich lessons
        path = prioritize_visual_content(path)

    return path
```

### Spiral Curriculum Path
```python
def generate_spiral_path(concept_family, grade_bands):
    """Generate spiral curriculum visiting concept at increasing depth."""
    path = []

    for band in grade_bands:
        # Get concept at this level
        concept_at_level = get_concept_at_band(concept_family, band)

        # Get lessons
        lessons = get_lessons(concept_at_level)

        # Add to path
        path.append(SpiralLevel(
            band=band,
            concept=concept_at_level,
            lessons=lessons,
            skill_level=band
        ))

    return SpiralPath(concept_family, path)
```

---

## Integration with Content Generation

### Content Generation Queries

When generating a lesson, the system queries:

1. **Prerequisites**: What must students already know?
2. **Connections**: What analogous concepts exist?
3. **Skills**: What skills does this develop?
4. **Assets**: What visualizations already exist?
5. **Progression**: What comes next?

```python
def prepare_lesson_context(lesson_id):
    """Gather all context for lesson generation."""
    lesson = graph.get_lesson(lesson_id)

    return LessonContext(
        prerequisites=graph.get_prerequisites(lesson.topic),
        connections=graph.get_cross_curricular(lesson.topic),
        skills=graph.get_skills(lesson.topic),
        existing_assets=graph.get_assets(lesson.topic),
        next_topics=graph.get_progression(lesson.topic),
        analogies=graph.get_analogies(lesson.concepts)
    )
```

### Dynamic Connection Highlighting

During content generation, the system automatically:
- References analogous concepts from other domains
- Uses consistent vocabulary across domains
- Explicitly names shared skills
- Links to related lessons in other subjects

---

## Storage & Query System

### Graph Database Selection

**Recommended**: Neo4j or Amazon Neptune

**Schema Implementation**:
```cypher
// Create constraints
CREATE CONSTRAINT concept_id ON (c:Concept) ASSERT c.id IS UNIQUE;
CREATE CONSTRAINT skill_id ON (s:Skill) ASSERT s.id IS UNIQUE;

// Create indexes
CREATE INDEX concept_name FOR (c:Concept) ON (c.name);
CREATE INDEX concept_domain FOR (c:Concept) ON (c.domain);
CREATE INDEX lesson_band FOR (l:Lesson) ON (l.grade_band);
```

### API Interface

```python
class KnowledgeGraphAPI:
    def get_concept(self, concept_id: str) -> Concept
    def get_prerequisites(self, concept_id: str, depth: int = 1) -> List[Concept]
    def get_analogies(self, concept_id: str) -> List[AnalogyConcept]
    def get_skills(self, concept_id: str) -> List[Skill]
    def get_assets(self, concept_id: str) -> List[Asset]
    def get_learning_path(self, start: str, end: str) -> List[Concept]
    def find_cross_curricular(self, concept_id: str) -> List[Connection]
    def get_progression(self, concept_id: str) -> List[Concept]
```

---

## Maintenance & Evolution

### Graph Updates
- New concepts added as curriculum evolves
- Connections refined based on learning analytics
- Asset links updated as new visualizations created
- Prerequisite strengths calibrated from student data

### Quality Assurance
- No orphan concepts (all connected)
- No circular prerequisites
- All concepts have at least one skill
- Cross-curricular connections validated by experts

---

*The Knowledge Graph is the connective tissue of the unified learning ecosystem, enabling true cross-curricular integration and skill-based learning progressions.*
