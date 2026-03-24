# Evaluation Framework For Pahl & Beitz Phase-Level Outputs

## Purpose
This framework adapts [EVALUATION_FRAMEWORK_PAHL_BEITZ.md](/Users/athulcd/Library/CloudStorage/OneDrive-purdue.edu/DELP%20Lab/Design%20Benchmarks/Systematic%20Experiments/Level%202/runs_gemini3_flash_stepwise_L2/EVALUATION_FRAMEWORK_PAHL_BEITZ.md) to the combined phase artifacts under `Phase/`, where multiple Pahl and Beitz outputs are merged into a single YAML file per phase.

Instead of evaluating each artifact separately, this framework evaluates each combined phase file by aggregating the relevant metrics and rubrics from the original artifact-level framework.

## Scope
- Target files:
  - `Phase/outputs/00_planning_combined.yaml`
  - `Phase/00_conceptual_design_combined.yaml`
  - `Phase/outputs/00_embodiment_design_combined.yaml`
  - `Phase/outputs/00_detail_design_combined.yaml`
- Goal: judge whether each combined file performs its full phase role in a Pahl and Beitz process, while remaining internally consistent across the sub-artifacts contained inside it.

## Phase Mapping
The combined phase files map to the original artifact-level framework as follows:

- `00_planning_combined.yaml`
  - `task_clarification`
  - `requirements_list`
- `00_conceptual_design_combined.yaml`
  - `black_box`
  - `function_structure`
  - `working_principles`
  - `concept_variants`
  - `concept_selection`
- `00_embodiment_design_combined.yaml`
  - `embodiment_layouts`
  - `preliminary_sizing`
  - `manufacturing_plan`
- `00_detail_design_combined.yaml`
  - `preliminary_bom`
  - `verification_plan`
  - `detail_design`

## Common Scoring Rules
### Objective metrics
For each phase file, use 5-10 metrics that can be computed directly from the YAML or from direct comparison to predecessor phase files.

For each metric, record:
- What to count or check.
- Why it matters in Pahl and Beitz terms.
- Direction: `higher is better`, `lower is better`, or `pass/fail`.

### Subjective rubric
Use a 1-5 scale for each rubric dimension:

- `1`: Poor or seriously inadequate for this phase.
- `2`: Weak and materially incomplete.
- `3`: Acceptable but limited.
- `4`: Good and mostly phase-appropriate.
- `5`: Strong, coherent, and phase-appropriate.

Do not reward verbosity, unsupported precision, or downstream detail that should belong to a later phase.

### LLM evaluator mode
Provide the LLM with:
- The combined phase YAML being evaluated.
- The immediately preceding combined phase file if one exists.
- The relevant phase system prompt from `Phase/logs/`.
- This phase-specific framework section.

Instruct the LLM to:
- Score strictly against the phase intent.
- Cite exact YAML keys, IDs, or fields as evidence.
- Check both internal consistency within the combined file and continuity with previous phases.
- Penalize empty placeholder sections that were supposed to be completed in the same pass.

### Human evaluator mode
Use the checklist below with emphasis on:
- Whether the combined file performs the whole phase role, not just one sub-artifact well.
- Whether later sections in the same file genuinely reuse IDs and outputs created earlier in that same file.
- Whether the phase output is appropriate in abstraction and maturity for Pahl and Beitz.

## 00_planning_combined.yaml
**Phase:** Planning

**Contains:** `task_clarification` + `requirements_list`

### Objective metrics
1. `Objective/constraint/evaluation-criteria coverage`
   - Count objectives, constraints, and evaluation criteria in `task_clarification`.
   - Matters because the planning phase should establish the design brief, not just restate the task.
   - Direction: higher is better up to meaningful coverage.
2. `Requirements count`
   - Count entries in `requirements_list`.
   - Matters because planning should convert the clarified task into an actionable specification.
   - Direction: descriptive.
3. `Requirement traceability density`
   - Average number of task-clarification IDs traced by each requirement.
   - Matters because requirements should derive from objectives, constraints, assumptions, or evaluation criteria already established in the same file.
   - Direction: higher is better if the links are meaningful.
4. `Requirements-per-constraint coverage`
   - Check whether the major constraints and evaluation criteria are represented by at least one requirement.
   - Matters because the planning phase should translate the brief into design-driving requirements.
   - Direction: pass/fail with notes.
5. `Open-question count`
   - Count unresolved questions under `task_clarification`.
   - Matters because planning should expose missing brief information early.
   - Direction: lower is better if key uncertainty is still captured honestly.
6. `Assumption explicitness`
   - Count assumptions and compare them with unresolved ambiguities in the task.
   - Matters because the phase should separate known facts from inferred operating assumptions.
   - Direction: descriptive.
7. `Specification completeness`
   - Fraction of requirements with clear descriptions and explicit traceability.
   - Matters because downstream conceptual design depends on a usable specification.
   - Direction: higher is better.

### Subjective rubric
1. `Task clarification quality`
   - `1`: Objectives, constraints, and criteria are missing or muddled.
   - `3`: Core planning content is present but incomplete or uneven.
   - `5`: The brief is clearly structured into objectives, constraints, evaluation criteria, assumptions, and open questions.
2. `Requirement quality`
   - `1`: Requirements are vague, redundant, or weakly connected to the brief.
   - `3`: Most requirements are usable, though some are broad or weakly specified.
   - `5`: Requirements are clear, design-relevant, and trace cleanly to the clarified task.
3. `Planning-phase appropriateness`
   - `1`: The file jumps into concepts or embodiment decisions too early.
   - `3`: Mostly planning-focused, with some premature solution detail.
   - `5`: The file defines what the tool must achieve without prematurely defining the concept.
4. `Internal consistency`
   - `1`: Objectives, constraints, and requirements conflict or do not line up.
   - `3`: Mostly consistent with some weak links.
   - `5`: Requirements logically derive from the planning content in the same file.
5. `Usefulness for conceptual design`
   - `1`: The next phase would still have to reconstruct the brief.
   - `3`: The file can support conceptual design, with some gaps.
   - `5`: The file provides a solid basis for black-box modeling and concept generation.

### LLM evaluator
- Check whether every requirement traces to items created in `task_clarification`.
- Penalize concept leakage and missing conversion of evaluation criteria into requirements.
- Compare against `Phase/logs/00_planning_system.txt` to verify that both planning sub-artifacts were actually completed.

### Human evaluator
- Ask whether this file would be enough to brief a conceptual-design team without extra explanation.
- Check whether the requirement set is genuinely derived from the planning content, not parallel to it.

## 00_conceptual_design_combined.yaml
**Phase:** Conceptual Design

**Contains:** `black_box` + `function_structure` + `working_principles` + `concept_variants` + `concept_selection`

### Objective metrics
1. `Black-box flow completeness`
   - Count input/output flows by type and check whether material, energy, and signal transformations are represented.
   - Matters because conceptual design should begin with the transformation system.
   - Direction: pass/fail with notes.
2. `Function decomposition coverage`
   - Count subfunctions and compare them against the black-box flows and planning requirements.
   - Matters because the overall function should be refined into actionable subfunctions.
   - Direction: higher is better if decomposition remains coherent.
3. `Flow reuse consistency`
   - Check whether function inputs/outputs reuse black-box flow IDs or consistent flow semantics.
   - Matters because function structure should refine, not replace, the black box.
   - Direction: higher is better.
4. `Working-principle coverage per subfunction`
   - Count principles per function and count functions with at least two alternatives.
   - Matters because conceptual design depends on alternative solution principles.
   - Direction: higher is better.
5. `Working-principle section completeness`
   - Check whether `working_principles.principles` is populated and tied to functions.
   - Matters because this phase prompt requires working principles before concept synthesis.
   - Direction: pass/fail.
6. `Concept variant diversity`
   - Count concepts and measure variation in selected principles across concepts.
   - Matters because concept synthesis should create genuinely different concept alternatives.
   - Direction: higher diversity is better.
7. `Concept-selection completeness`
   - Check whether the comparison matrix covers all concepts and whether a selected concept and rationale are present.
   - Matters because the phase should end with a justified concept choice.
   - Direction: pass/fail.
8. `Concept-selection traceability`
   - Verify that the selected concept exists in `concept_variants` and is supported by the comparison matrix and rationale.
   - Matters because concept selection should be auditable.
   - Direction: pass/fail.
9. `Requirements coverage across the phase`
   - Count requirement IDs referenced in function structure, concept reasoning, or concept-selection rationale.
   - Matters because conceptual design should remain tied to the planning specification.
   - Direction: higher is better.

### Subjective rubric
1. `Transformation-to-function coherence`
   - `1`: Black box and function structure are disconnected or inconsistent.
   - `3`: Main logic is understandable, though flow continuity is uneven.
   - `5`: The black box, function structure, and concept space form a coherent conceptual chain.
2. `Conceptual breadth`
   - `1`: Little real alternative generation is present.
   - `3`: Some alternatives exist but are narrow or uneven.
   - `5`: The file explores a credible range of working principles and concept variants.
3. `Concept-selection rigor`
   - `1`: Selection is arbitrary or weakly justified.
   - `3`: A choice is made with some comparison evidence.
   - `5`: The selected concept follows clearly from documented concept tradeoffs and evaluation criteria.
4. `Phase completeness`
   - `1`: One or more required conceptual sub-artifacts are missing or effectively empty.
   - `3`: All major pieces exist but one is thin or weakly integrated.
   - `5`: All conceptual sub-artifacts are present and substantively connected.
5. `Appropriate abstraction level`
   - `1`: The file jumps prematurely into embodiment or detailed part choices.
   - `3`: Mostly conceptual, with some downstream leakage.
   - `5`: The file stays at conceptual-design level while still being specific enough to choose a concept.

### LLM evaluator
- Compare the file against `Phase/logs/00_conceptual_design_system.txt`.
- Explicitly flag if `working_principles` is empty or if concept variants reference working-principle IDs that were never defined.
- Check the chain: black box -> function structure -> working principles -> concept variants -> concept selection.

### Human evaluator
- Ask whether this file truly completes conceptual design or whether one of the sub-stages was skipped.
- Check whether the chosen concept feels like the result of systematic concept work rather than a direct jump from function to selection.

## 00_embodiment_design_combined.yaml
**Phase:** Embodiment Design

**Contains:** `embodiment_layouts` + `preliminary_sizing` + `manufacturing_plan`

### Objective metrics
1. `Module/interface coverage`
   - Count modules and interfaces in `embodiment_layouts`.
   - Matters because embodiment design should partition the selected concept into physical subsystems and interfaces.
   - Direction: higher is better up to relevance.
2. `Function allocation coverage`
   - Fraction of modules with allocated functions or clear relation to prior conceptual outputs.
   - Matters because embodiment should still reflect functional intent.
   - Direction: higher is better.
3. `Key design parameter coverage`
   - Count KDPs and compare them with the main embodiment risks or layout decisions.
   - Matters because embodiment should identify governing physical parameters.
   - Direction: higher is better.
4. `Sizing-result coverage`
   - Count load cases, KDP sizing results, and fraction of KDPs that receive explicit sizing treatment.
   - Matters because embodiment should move from layout ideas toward first-order engineering feasibility.
   - Direction: higher is better.
5. `Sizing-to-layout consistency`
   - Check whether sizing references KDPs and module choices actually defined in the layout section.
   - Matters because preliminary sizing should elaborate the chosen embodiment.
   - Direction: pass/fail.
6. `Manufacturing-plan coverage`
   - Count work packages or critical components and compare them with embodiment modules.
   - Matters because manufacturing planning should derive from the embodied architecture.
   - Direction: higher is better.
7. `Criticality awareness`
   - Count critical components and specific manufacturing or calibration concerns.
   - Matters because embodiment should identify technically sensitive parts before detail design.
   - Direction: higher is better if well justified.
8. `Cross-section ID consistency`
   - Verify consistency of module IDs, KDP IDs, and other identifiers across embodiment, sizing, and manufacturing sections.
   - Matters because this combined phase is supposed to be internally coherent in one pass.
   - Direction: pass/fail.

### Subjective rubric
1. `Embodiment realism`
   - `1`: The layout is too abstract or physically implausible.
   - `3`: A plausible preliminary layout exists, though some interfaces or physical details are thin.
   - `5`: The embodiment is physically credible and aligned with the selected concept.
2. `Sizing adequacy`
   - `1`: Sizing is superficial or disconnected from the embodiment.
   - `3`: Core first-order checks exist but do not cover all governing issues.
   - `5`: Sizing gives meaningful first-order confidence in the embodiment.
3. `Manufacturing readiness`
   - `1`: Manufacturing content is generic or disconnected from the design.
   - `3`: Work packages and critical components are plausible.
   - `5`: Manufacturing thinking clearly supports the embodied architecture and likely production route.
4. `Internal phase integration`
   - `1`: Layout, sizing, and manufacturing read like unrelated fragments.
   - `3`: The sections mostly align, with some inconsistency.
   - `5`: The three sections build on one another coherently within the same file.
5. `Appropriate phase maturity`
   - `1`: The file is either still too conceptual or prematurely detailed.
   - `3`: Mostly appropriate maturity.
   - `5`: This is recognizably embodiment design: physical layout plus first-order sizing and manufacturing implications.

### LLM evaluator
- Compare against both `Phase/00_conceptual_design_combined.yaml` and `Phase/logs/00_embodiment_design_system.txt`.
- Check that the embodiment develops the selected concept, not a different architecture.
- Penalize inconsistent module numbering or manufacturing content that introduces ungrounded components.

### Human evaluator
- Ask whether the phase file reduces technical uncertainty enough to support BOM and verification planning.
- Check whether the critical physical decisions and risks are explicit.

## 00_detail_design_combined.yaml
**Phase:** Detail Design

**Contains:** `preliminary_bom` + `verification_plan` + `detail_design`

### Objective metrics
1. `BOM completeness`
   - Count BOM items and distinct module IDs represented.
   - Matters because detail design should express the embodiment as concrete components.
   - Direction: higher is better up to full subsystem coverage.
2. `BOM field completeness`
   - Fraction of items with non-empty name, module ID, material, quantity, and manufacturing source.
   - Matters because the BOM should be usable for downstream fabrication and review.
   - Direction: higher is better.
3. `Verification coverage`
   - Count verification activities and compare covered requirement IDs with planning-phase requirements.
   - Matters because detail design should define how the design will be checked.
   - Direction: higher is better; full MUST-equivalent coverage is preferred.
4. `Method and acceptance-criteria completeness`
   - Fraction of verification activities with explicit method and acceptance criteria.
   - Matters because verification should be executable, not only aspirational.
   - Direction: higher is better.
5. `BOM-to-verification linkage`
   - Check whether verification activities align with actual BOM items or detail-design elements where appropriate.
   - Matters because testing should relate to the realized design.
   - Direction: pass/fail with notes.
6. `Detail-design specification density`
   - Count key specifications, assembly instructions, maintenance requirements, assumptions, and open questions.
   - Matters because this phase should consolidate the design into a usable detailed stub.
   - Direction: higher is better up to relevance.
7. `Cross-section consistency`
   - Check consistency between selected concept, embodiment architecture, BOM contents, verification activities, and detail-design narrative.
   - Matters because the combined detail-design file should read as one coherent downstream elaboration.
   - Direction: pass/fail.
8. `Requirements-reference continuity`
   - Count planning requirements referenced in verification activities or detail-design specifications.
   - Matters because detail design should remain traceable to the original specification.
   - Direction: higher is better.

### Subjective rubric
1. `Componentization quality`
   - `1`: The BOM misses major elements or uses vague part definitions.
   - `3`: Main components are present, though some detail is thin.
   - `5`: The BOM gives a credible component-level view of the design.
2. `Verification rigor`
   - `1`: Verification activities are vague or incomplete.
   - `3`: Main tests and inspections are present.
   - `5`: Verification activities credibly cover key requirements with usable criteria.
3. `Detail-design coherence`
   - `1`: The detail-design summary contradicts the BOM, verification plan, or prior phases.
   - `3`: Mostly coherent with some gaps or mismatches.
   - `5`: The file reads as a consistent detail-design completion stub.
4. `Downstream usefulness`
   - `1`: The file would not help fabrication, assembly, or validation planning.
   - `3`: It provides a usable starting point.
   - `5`: It supports fabrication planning, assembly understanding, and validation planning in a concrete way.
5. `Appropriate phase maturity`
   - `1`: Still too conceptual or too incomplete for detail design.
   - `3`: Reasonably mature but still clearly a stub.
   - `5`: A strong detail-design stub that is consistent with prior phases and useful for handoff.

### LLM evaluator
- Compare against `Phase/outputs/00_embodiment_design_combined.yaml` and `Phase/logs/00_detail_design_system.txt`.
- Check whether BOM items, verification activities, and the detail-design summary refer to the same architecture.
- Penalize ID drift, unsupported module IDs, and verification claims that cannot be connected back to requirements or components.

### Human evaluator
- Ask whether this file would help a downstream engineer start detailed drawings, fabrication planning, and verification prep.
- Check whether the file remains a coherent elaboration of the chosen concept and embodiment rather than a new design.

## Cross-Phase Evaluation Chain
Use the following continuity checks:

1. `Planning -> Conceptual Design`
   - Confirm conceptual design uses the planning objectives, constraints, and requirements rather than creating a disconnected problem frame.
2. `Conceptual Design -> Embodiment Design`
   - Confirm embodiment develops the selected concept and preserves the main functional and concept decisions.
3. `Embodiment Design -> Detail Design`
   - Confirm the BOM, verification plan, and detail-design summary elaborate the embodied architecture rather than replacing it.
4. `End-to-end requirements continuity`
   - Confirm requirement IDs or equivalent planning demands remain visible through concept selection, embodiment sizing/manufacturing choices, and verification activities.

## Recommended Evaluation Record Template
Use this template for each combined phase file:

```markdown
### <phase file name>
Phase:

Contained sub-artifacts:
- 

Objective metrics:
- Metric:
  - Result:
  - Interpretation:

Subjective rubric:
- Dimension:
  - Score (1-5):
  - Evidence:

LLM evaluator notes:
- 

Human evaluator notes:
- 

Cross-phase issues:
- 
```

## Validation Checklist For This Framework
- Each section names the correct combined phase file and the phase it represents.
- Each section merges the correct artifact-level concerns from the original framework.
- Every objective metric is computable from the combined YAML or direct phase-to-phase comparison.
- Every rubric dimension evaluates full-phase performance, not just one sub-artifact in isolation.
- Cross-phase checks cover planning, conceptual design, embodiment design, and detail design continuity.
