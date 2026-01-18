# Visualization Asset Management System

A comprehensive system for storing, cataloging, reusing, and adapting visual assets across the unified learning ecosystem.

---

## Core Philosophy: Reuse First, Generate Last

```
┌─────────────────────────────────────────────────────────────┐
│              ASSET ACQUISITION PRIORITY                      │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  1. REUSE EXACT     → Same asset, already in repository      │
│         ↓                                                    │
│  2. ADAPT EXISTING  → Modify existing asset (labels, colors) │
│         ↓                                                    │
│  3. SOURCE EXTERNAL → Find from licensed libraries           │
│         ↓                                                    │
│  4. GENERATE NEW    → Create with AI (last resort)           │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

**Rationale**:
- Consistency across curriculum
- Quality control (vetted assets)
- Cost efficiency
- Faster production
- Reduced hallucination risk in visuals

---

## Asset Repository Architecture

### Repository Structure

```
assets/
├── library/                    # Main asset library
│   ├── mathematics/
│   │   ├── algebra/
│   │   │   ├── linear-functions/
│   │   │   │   ├── slope-triangle-001.png
│   │   │   │   ├── slope-triangle-001.svg
│   │   │   │   ├── slope-triangle-001.meta.yaml
│   │   │   │   └── ...
│   │   │   ├── quadratics/
│   │   │   └── systems/
│   │   ├── geometry/
│   │   └── calculus/
│   ├── science/
│   │   ├── biology/
│   │   ├── chemistry/
│   │   └── physics/
│   ├── history/
│   ├── language-arts/
│   └── shared/                 # Cross-domain assets
│       ├── graphs/
│       ├── charts/
│       ├── icons/
│       └── templates/
├── generated/                  # AI-generated assets
│   └── pending-review/         # Not yet approved
├── external/                   # Sourced from external libraries
│   ├── wikimedia/
│   ├── openstax/
│   └── custom/
└── adaptations/                # Modified versions of existing
```

### Asset Metadata Schema

```yaml
# slope-triangle-001.meta.yaml
asset:
  id: "asset:math:slope-triangle-001"
  version: "2.1"
  created: "2025-01-15"
  modified: "2025-03-22"

  # Core identification
  title: "Slope Triangle on Coordinate Plane"
  description: "Coordinate plane showing line with slope triangle, rise and run labeled"

  # File information
  files:
    primary: "slope-triangle-001.png"
    vector: "slope-triangle-001.svg"
    source: "slope-triangle-001.ai"  # Adobe Illustrator source
  format: "PNG"
  dimensions:
    width: 2400
    height: 1800
  resolution: 300  # DPI
  color_space: "RGB"

  # Content classification
  domain: "mathematics"
  subdomain: "algebra"
  topic: "linear-functions"
  concepts:
    - "concept:slope"
    - "concept:rate_of_change"
    - "concept:coordinate_plane"

  # Skill connections
  skills:
    - "skill:visualization"
    - "skill:proportional_reasoning"

  # Educational metadata
  grade_bands: [3, 4, 5]  # Appropriate for middle school through college
  complexity: "medium"
  abstraction: "low"

  # Visual characteristics
  style: "technical"
  colors:
    primary: "#2E86AB"
    secondary: "#E94F37"
    background: "#FFFFFF"
  accessibility:
    colorblind_safe: true
    high_contrast: true
    has_alt_text: true

  # Adaptation information
  adaptable: true
  adaptation_points:
    - type: "label"
      location: "rise_label"
      current: "rise = 3"
      editable: true
    - type: "label"
      location: "run_label"
      current: "run = 4"
      editable: true
    - type: "color"
      location: "line_color"
      current: "#2E86AB"
      editable: true
    - type: "value"
      location: "point_coordinates"
      current: "(1,2) and (5,5)"
      editable: true

  # Usage tracking
  usage:
    count: 47
    lessons:
      - "lesson:math:slope-intro"
      - "lesson:math:slope-applications"
      - "lesson:science:velocity-intro"
    last_used: "2025-03-20"

  # Licensing
  license:
    type: "CC-BY-4.0"
    attribution: "Textbook Builder Project"
    commercial: true
    modifications: true

  # Quality
  quality_score: 0.92
  reviewed: true
  reviewer: "visual-qa-team"
  review_date: "2025-02-10"

  # Relations
  related_assets:
    - "asset:math:slope-types-001"
    - "asset:math:coordinate-plane-blank"
  parent: null  # Not an adaptation
  adaptations:
    - "asset:math:slope-triangle-002"  # Different colors
    - "asset:math:slope-triangle-003"  # Spanish labels
```

---

## Asset Search & Discovery

### Multi-Modal Search

```python
class AssetSearchEngine:
    def __init__(self, repository):
        self.repo = repository
        self.text_index = self.build_text_index()
        self.embedding_index = self.build_embedding_index()

    def search(self, query, mode="hybrid", filters=None):
        """
        Search for assets using multiple strategies.

        Modes:
        - text: Keyword/metadata search
        - semantic: Embedding-based similarity
        - visual: Image similarity
        - hybrid: Combination of all
        """
        results = []

        if mode in ["text", "hybrid"]:
            text_results = self.text_search(query, filters)
            results.extend(text_results)

        if mode in ["semantic", "hybrid"]:
            semantic_results = self.semantic_search(query, filters)
            results.extend(semantic_results)

        if mode == "visual" and isinstance(query, Image):
            visual_results = self.visual_search(query, filters)
            results.extend(visual_results)

        # Deduplicate and rank
        ranked = self.rank_results(results)
        return ranked

    def text_search(self, query, filters):
        """Search by keywords in metadata."""
        return self.text_index.search(
            query=query,
            fields=["title", "description", "concepts", "topics"],
            filters=filters
        )

    def semantic_search(self, query, filters):
        """Search by semantic similarity."""
        query_embedding = self.embed_text(query)
        return self.embedding_index.nearest_neighbors(
            query_embedding,
            k=20,
            filters=filters
        )

    def visual_search(self, image, filters):
        """Search by visual similarity to reference image."""
        image_embedding = self.embed_image(image)
        return self.embedding_index.nearest_neighbors(
            image_embedding,
            k=20,
            filters=filters,
            index="visual"
        )
```

### Query Examples

```python
# Find assets for teaching slope
results = asset_search.search(
    query="slope triangle coordinate plane",
    filters={
        "domain": "mathematics",
        "grade_bands": {"$contains": 4},
        "adaptable": True
    }
)

# Find assets similar to existing one
reference_asset = repo.get("asset:math:slope-triangle-001")
similar = asset_search.search(
    query=reference_asset.image,
    mode="visual",
    filters={"domain": "mathematics"}
)

# Semantic search for concept
results = asset_search.search(
    query="diagram showing how y changes as x increases",
    mode="semantic"
)
```

---

## Asset Adaptation System

### Adaptation Types

| Type | Description | Automation Level |
|------|-------------|------------------|
| Label change | Modify text labels | Fully automated |
| Value change | Update numerical values | Fully automated |
| Color change | Adjust colors for branding/accessibility | Fully automated |
| Translation | Change language of labels | Partially automated |
| Simplification | Reduce complexity for younger grades | Manual |
| Extension | Add elements for older grades | Manual |
| Style change | Change visual style | Manual |

### Automated Adaptation Pipeline

```python
class AssetAdapter:
    def __init__(self, asset):
        self.asset = asset
        self.source = self.load_source(asset)

    def adapt(self, adaptations):
        """
        Apply adaptations to create new asset version.

        Args:
            adaptations: List of adaptation specifications

        Returns:
            New asset with adaptations applied
        """
        adapted = self.source.copy()

        for adaptation in adaptations:
            if adaptation.type == "label":
                adapted = self.change_label(
                    adapted,
                    adaptation.location,
                    adaptation.new_value
                )
            elif adaptation.type == "color":
                adapted = self.change_color(
                    adapted,
                    adaptation.location,
                    adaptation.new_value
                )
            elif adaptation.type == "value":
                adapted = self.change_value(
                    adapted,
                    adaptation.location,
                    adaptation.new_value
                )
            elif adaptation.type == "translation":
                adapted = self.translate(
                    adapted,
                    adaptation.target_language
                )

        # Create new asset record
        new_asset = self.create_adapted_asset(adapted, adaptations)
        return new_asset

    def change_label(self, source, location, new_value):
        """Change text label in SVG or editable format."""
        if source.format == "SVG":
            # Find text element by ID
            element = source.find_by_id(location)
            element.text = new_value
        elif source.format in ["AI", "PSD"]:
            # Use layer name
            layer = source.get_layer(location)
            layer.text = new_value

        return source

    def change_color(self, source, location, new_color):
        """Change color in vector or raster image."""
        if source.format == "SVG":
            element = source.find_by_id(location)
            element.set_fill(new_color)
        else:
            # Raster: use color replacement
            source = self.replace_color(
                source,
                self.asset.colors[location],
                new_color
            )
        return source

    def create_adapted_asset(self, adapted, adaptations):
        """Create metadata for adapted asset."""
        new_id = f"{self.asset.id}-adapted-{uuid.uuid4().hex[:8]}"

        return Asset(
            id=new_id,
            parent=self.asset.id,
            version="1.0",
            adaptations=adaptations,
            # Inherit most metadata from parent
            domain=self.asset.domain,
            concepts=self.asset.concepts,
            skills=self.asset.skills,
            # New file
            file=self.export(adapted),
        )
```

### Adaptation Request

```python
# Example: Adapt slope diagram for different example
adaptation_request = [
    {
        "type": "label",
        "location": "rise_label",
        "new_value": "rise = 6"
    },
    {
        "type": "label",
        "location": "run_label",
        "new_value": "run = 2"
    },
    {
        "type": "value",
        "location": "slope_result",
        "new_value": "slope = 3"
    },
    {
        "type": "color",
        "location": "line_color",
        "new_value": "#E94F37"
    }
]

adapted_asset = adapter.adapt(adaptation_request)
```

---

## Cross-Curricular Asset Sharing

### Shared Concept Visualization

The same diagram can serve multiple domains:

```yaml
# assets/shared/graphs/exponential-growth-001.meta.yaml
asset:
  id: "asset:shared:exponential-growth-001"
  title: "Exponential Growth Curve"

  # Used across domains
  domains:
    - domain: "mathematics"
      topic: "exponential-functions"
      lesson_context: "Graphing exponential functions"

    - domain: "biology"
      topic: "population-dynamics"
      lesson_context: "Bacterial growth illustration"

    - domain: "finance"
      topic: "compound-interest"
      lesson_context: "Investment growth over time"

    - domain: "epidemiology"
      topic: "disease-spread"
      lesson_context: "Early-stage infection rates"

  # Adaptation variants for each domain
  domain_variants:
    mathematics:
      labels: ["y = 2^x", "x", "y"]
      colors: "#2E86AB"

    biology:
      labels: ["Population", "Time (hours)", "Bacteria Count"]
      colors: "#27AE60"

    finance:
      labels: ["Value ($)", "Years", "Investment"]
      colors: "#F39C12"

    epidemiology:
      labels: ["Cases", "Days", "Infections"]
      colors: "#E74C3C"
```

### Cross-Curricular Discovery

```python
def find_cross_curricular_assets(concept_id):
    """Find assets used across multiple domains for same underlying concept."""
    # Get concept from knowledge graph
    concept = knowledge_graph.get_concept(concept_id)

    # Find analogous concepts in other domains
    analogies = knowledge_graph.get_analogies(concept_id)

    # Search for assets tagged with any of these concepts
    all_concepts = [concept_id] + [a.id for a in analogies]

    assets = asset_search.search(
        filters={"concepts": {"$in": all_concepts}}
    )

    # Group by asset
    shared_assets = group_by_shared_usage(assets)

    return shared_assets
```

---

## External Asset Sourcing

### Supported Sources

| Source | License | Content Type |
|--------|---------|-------------|
| Wikimedia Commons | Various (CC, PD) | Photos, diagrams |
| OpenStax | CC-BY | Textbook figures |
| PhET Simulations | CC-BY | Interactive simulations |
| Khan Academy | CC-BY-NC-SA | Diagrams |
| Unsplash | Unsplash License | Photos |
| Creative Commons Search | Various CC | Mixed |

### Source Integration

```python
class ExternalAssetSourcer:
    def __init__(self):
        self.sources = {
            "wikimedia": WikimediaAPI(),
            "openstax": OpenStaxAPI(),
            "unsplash": UnsplashAPI(),
        }

    def search_external(self, query, filters=None):
        """Search across external sources."""
        results = []

        for name, source in self.sources.items():
            try:
                source_results = source.search(query)
                results.extend([
                    ExternalAsset(
                        source=name,
                        **r
                    ) for r in source_results
                ])
            except Exception as e:
                logger.warning(f"Failed to search {name}: {e}")

        return results

    def import_asset(self, external_asset):
        """Import external asset into local repository."""
        # Download file
        file_path = self.download(external_asset.url)

        # Verify license
        if not self.verify_license(external_asset.license):
            raise LicenseError(f"License {external_asset.license} not allowed")

        # Create local asset record
        local_asset = Asset(
            id=self.generate_id(external_asset),
            source="external",
            external_source=external_asset.source,
            external_id=external_asset.id,
            license=external_asset.license,
            attribution=external_asset.attribution,
            file=file_path,
            # Metadata needs manual review
            needs_review=True
        )

        return local_asset
```

---

## AI Generation Pipeline

### When to Generate

Generate new assets only when:
1. No suitable existing asset found
2. No adaptable asset found
3. External sourcing failed
4. Specific custom requirement

### Generation Workflow

```python
class AssetGenerator:
    def __init__(self, model="dall-e-3"):
        self.model = model
        self.style_guide = load_style_guide()

    def generate(self, specification):
        """Generate new asset from specification."""
        # Build prompt from specification
        prompt = self.build_prompt(specification)

        # Apply style constraints
        prompt = self.apply_style_guide(prompt)

        # Generate image
        result = self.model.generate(
            prompt=prompt,
            size=specification.dimensions,
            quality="hd",
            style="natural"
        )

        # Create asset record
        asset = Asset(
            id=self.generate_id(),
            source="generated",
            generation_prompt=prompt,
            model=self.model.name,
            file=result.save(),
            needs_review=True,  # Always requires review
            concepts=specification.concepts,
            skills=specification.skills,
        )

        return asset

    def build_prompt(self, spec):
        """Build generation prompt from specification."""
        prompt = f"""
Educational diagram for {spec.grade_level} students.

Content: {spec.description}

Required elements:
{chr(10).join(f'- {e}' for e in spec.required_elements)}

Style requirements:
- Clean, professional educational illustration
- Clear labels in {spec.label_font}
- High contrast for readability
- No decorative elements
- Accurate proportions

Color scheme: {spec.color_scheme}
"""
        return prompt
```

### Generation Quality Control

```python
class GeneratedAssetReviewer:
    def review(self, asset):
        """Review generated asset for quality and accuracy."""
        checks = [
            self.check_accuracy,
            self.check_clarity,
            self.check_labels,
            self.check_accessibility,
            self.check_style_compliance,
        ]

        issues = []
        for check in checks:
            result = check(asset)
            if not result.passed:
                issues.append(result.issue)

        if issues:
            asset.status = "needs_revision"
            asset.review_notes = issues
        else:
            asset.status = "approved"
            asset.needs_review = False

        return asset
```

---

## Caption & Alt-Text System

### Automatic Generation

```python
class CaptionGenerator:
    def generate_caption(self, asset, context):
        """Generate educational caption for asset."""
        prompt = f"""
Generate a caption for this educational figure.

Asset description: {asset.description}
Context: {context.lesson_title}
Learning objective: {context.objective}
Grade level: {context.grade_band}

Requirements:
- Start with "Figure X.Y:"
- Describe what the figure shows
- Connect to learning objective
- {context.grade_band_appropriate_language}
- Maximum 2 sentences
"""
        return self.model.generate(prompt)

    def generate_alt_text(self, asset):
        """Generate accessibility alt-text."""
        prompt = f"""
Generate detailed alt-text for this educational image.

Description: {asset.description}
Elements: {asset.elements}

Requirements:
- Describe ALL visual information
- Include text/labels verbatim
- Describe spatial relationships
- Include colors if meaningful
- Maximum 250 characters
- Useful for screen reader users
"""
        return self.model.generate(prompt)
```

### Caption Templates by Type

```yaml
figure_captions:
  diagram:
    template: "Figure {num}: {description}. {connection_to_concept}"
    example: "Figure 3.1: Slope triangle showing rise over run. The vertical change (rise) is 3 units while the horizontal change (run) is 4 units."

  graph:
    template: "Figure {num}: {title}. {axis_description}. {key_observation}"
    example: "Figure 2.5: Linear function y = 2x + 1. The x-axis shows input values from -5 to 5, and the y-axis shows output values. Notice the constant slope of 2."

  photograph:
    template: "Figure {num}: {subject}. {relevance}"
    example: "Figure 4.2: Honeycomb structure in a beehive. This natural example demonstrates hexagonal tessellation."

  table:
    template: "Table {num}: {title}. {content_summary}"
    example: "Table 1.3: Properties of geometric shapes. Compares sides, angles, and symmetry for common polygons."
```

---

## Usage Analytics

### Tracking Asset Performance

```python
class AssetAnalytics:
    def track_usage(self, asset_id, context):
        """Track asset usage for analytics."""
        self.db.insert({
            "asset_id": asset_id,
            "lesson_id": context.lesson_id,
            "domain": context.domain,
            "grade_band": context.grade_band,
            "timestamp": datetime.now(),
        })

    def get_usage_stats(self, asset_id):
        """Get usage statistics for asset."""
        return {
            "total_uses": self.count_uses(asset_id),
            "domains": self.get_domains_used(asset_id),
            "grade_bands": self.get_grade_bands_used(asset_id),
            "lessons": self.get_lessons_used(asset_id),
            "last_used": self.get_last_used(asset_id),
        }

    def find_underutilized(self):
        """Find high-quality assets that could be used more."""
        return self.db.query(
            quality_score > 0.8,
            usage_count < 5,
            adaptable == True
        )

    def find_overused(self):
        """Find assets that might need variants."""
        return self.db.query(
            usage_count > 50,
            variants_count < 3
        )
```

---

## Repository Maintenance

### Quality Auditing

```python
class AssetAuditor:
    def audit_repository(self):
        """Run comprehensive audit of asset repository."""
        audits = [
            self.audit_metadata_completeness,
            self.audit_file_integrity,
            self.audit_resolution_compliance,
            self.audit_accessibility,
            self.audit_license_compliance,
            self.audit_orphaned_assets,
            self.audit_duplicate_detection,
        ]

        report = AuditReport()
        for audit in audits:
            result = audit()
            report.add(result)

        return report

    def audit_accessibility(self):
        """Check all assets meet accessibility standards."""
        issues = []
        for asset in self.repo.all():
            if not asset.has_alt_text:
                issues.append(f"{asset.id}: Missing alt-text")
            if not asset.colorblind_safe:
                issues.append(f"{asset.id}: Not colorblind safe")
        return AuditResult("Accessibility", issues)
```

---

## Integration with Content Pipeline

### Asset Selection During Content Generation

```python
def select_asset_for_concept(concept, context):
    """Select best asset for a concept in given context."""
    # 1. Search for exact match
    candidates = asset_search.search(
        query=concept.name,
        filters={
            "concepts": {"$contains": concept.id},
            "grade_bands": {"$contains": context.grade_band}
        }
    )

    if candidates:
        return select_best_match(candidates, context)

    # 2. Search for adaptable asset
    adaptable = asset_search.search(
        query=concept.name,
        filters={
            "concepts": {"$contains": concept.id},
            "adaptable": True
        }
    )

    if adaptable:
        best = select_best_match(adaptable, context)
        return create_adaptation(best, context)

    # 3. Search external sources
    external = external_sourcer.search(concept.name)
    if external:
        return import_and_adapt(external[0], context)

    # 4. Generate new asset
    return generate_new_asset(concept, context)
```

---

*The Asset Management System ensures consistent, high-quality visualizations across the unified learning ecosystem while minimizing generation costs and maintaining educational accuracy.*
