# Evaluation Framework For Pahl & Beitz Stage Outputs

## Purpose
This framework defines consistent evaluation criteria for the 13 canonical top-level `*_model.yaml` artifacts in the fixed Pahl and Beitz sequence. It is grounded in the intended Pahl and Beitz stage represented by each artifact, not just the literal prompt-log filename.

Use it to evaluate outputs objectively where the YAML permits direct counting or checking, and subjectively where quality depends on stage-appropriate design judgment.

## Scope
- Target artifacts: the 13 canonical top-level `*_model.yaml` files in this directory, from `01_task_clarification_model.yaml` through `13_detail_design_model.yaml`.
- Non-target artifacts: `inputs/*.yaml`, `logs/*.txt`, `logs/*.json`, and `*_report.json`.
- Evaluation goal: assess artifact quality as a staged systematic design output, not as general prose.

## Prompt Log Offset
The artifact filenames are canonical and fixed. The `inputs/` and `logs/` files may still reflect execution-local counters or the previous-step packet naming convention.

When grounding an evaluation in packets or prompt logs:
- Select the rubric by the artifact filename being evaluated, not by the neighboring log filename.
- Use the artifact's stage intent and predecessor outputs as the primary evidence chain.
- If the expected packet or prompt-side evidence is missing, flag that explicitly as an evidence limitation.

## Common Scoring Rules
### Objective metrics
For each artifact, use 4-8 metrics that can be computed directly from the YAML or from direct cross-file comparison with predecessor artifacts.

For each metric, record:
- What to count or check.
- Why it matters in Pahl and Beitz terms.
- Direction: `higher is better`, `lower is better`, or `pass/fail`.

### Subjective rubric
Use a 1-5 scale for each rubric dimension:

- `1`: Poor or seriously inadequate for this stage.
- `2`: Weak and materially incomplete.
- `3`: Acceptable but limited.
- `4`: Good and mostly stage-appropriate.
- `5`: Strong, well-structured, and stage-appropriate.

Do not reward verbosity, speculative detail, or downstream design detail that should not exist yet at the current stage.

### LLM evaluator mode
Provide the LLM with:
- The current `*_model.yaml`.
- The immediately relevant predecessor artifact(s).
- The stage-specific rubric below.

Instruct the LLM to:
- Score strictly against stage intent.
- Cite exact YAML keys, IDs, or fields for every score.
- Flag invented precision, solution bias introduced too early, broken traceability, and cross-file inconsistencies.
- Avoid rewarding length, polish, or jargon.

### Human evaluator mode
Use the stage-specific checklist below with emphasis on:
- Fit to the intended Pahl and Beitz stage.
- Traceability to earlier artifacts.
- Appropriate abstraction level.
- Internal consistency and usefulness for the next stage.

### Missing-file policy
- If the primary `*_model.yaml` file for a stage is missing, record that as a missing expected artifact and skip scored evaluation for that stage.
- If predecessor or packet evidence files are missing, still evaluate the primary artifact but flag the missing evidence under `Cross-file issues` and in the report summary.

## 01_task_clarification_model.yaml
**Stage:** Task clarification / clarified design brief

### Objective metrics
1. `Objective count`
   - Count `task_clarification.objectives`.
   - Matters because task clarification should make the design intent explicit before requirements are formalized.
   - Direction: higher is better up to clear coverage of the brief.
2. `Constraint count by class`
   - Count `task_clarification.constraints` and summarize the `type` distribution.
   - Matters because the brief should surface governing limits before concept generation begins.
   - Direction: descriptive.
3. `System-boundary completeness`
   - Check whether both `system_boundary.included` and `system_boundary.excluded` are populated.
   - Matters because Pahl and Beitz starts by clarifying what is and is not in scope.
   - Direction: pass/fail with notes.
4. `Evaluation-criteria count`
   - Count `task_clarification.evaluation_criteria`.
   - Matters because later concept selection depends on explicit criteria rooted in the clarified task.
   - Direction: higher is better up to relevance.
5. `Open-question count`
   - Count `task_clarification.open_questions`.
   - Matters because unresolved brief ambiguities should be exposed rather than silently invented away.
   - Direction: descriptive.
6. `Traceability declaration`
   - Check whether `traceability.derived_from` references `raw_task_statement`.
   - Matters because this artifact should be a structured interpretation of the given brief, not a fresh invention.
   - Direction: pass/fail.

### Subjective rubric
1. `Problem framing quality`
   - `1`: The problem statement is vague, incomplete, or misframed.
   - `3`: The core design problem is understandable but unevenly scoped.
   - `5`: The design problem is clearly framed, decision-relevant, and appropriately bounded.
2. `Boundary clarity`
   - `1`: In-scope and out-of-scope elements are mixed or absent.
   - `3`: Boundary is partially clear but some exclusions or interfaces remain fuzzy.
   - `5`: System boundary is explicit and supports later decomposition.
3. `Stakeholder and operating-context coverage`
   - `1`: Key stakeholders or operating conditions are missing.
   - `3`: Main context is present but some use conditions or parties are thin.
   - `5`: Stakeholders and operating conditions are well captured and useful downstream.
4. `Evaluation readiness`
   - `1`: Criteria are too vague to guide concept assessment later.
   - `3`: Some criteria are useful, others remain broad.
   - `5`: Criteria are concrete enough to support later tradeoff work.
5. `Solution neutrality`
   - `1`: The brief already commits to specific solution choices.
   - `3`: Mostly neutral, with minor concept leakage.
   - `5`: Stays focused on what must be achieved, not how.

### LLM evaluator
- Compare against `inputs/00_raw_task_statement_packet.yaml` when available.
- Check whether the artifact surfaces objectives, constraints, exclusions, assumptions, and open questions without drifting into concept proposals.
- Penalize invented stakeholder needs, fabricated quantified targets, and premature embodiment content.

### Human evaluator
- Ask whether this reads like a clarified brief that a design team could align around before writing requirements.
- Check whether the exclusions and open questions would reduce downstream ambiguity.

## 02_requirements_list_model.yaml
**Stage:** Requirements list / design specification

### Objective metrics
1. `Total requirements`
   - Count all entries under `requirements_list.requirements`.
   - Matters because a requirements list should explicitly capture the design brief in structured form.
   - Direction: higher is better up to coverage sufficiency; extremely high counts can indicate fragmentation.
2. `MUST vs WISH distribution`
   - Count requirements by `priority`.
   - Matters because Pahl and Beitz distinguishes binding demands from wishes for later concept tradeoffs.
   - Direction: descriptive, not inherently better high or low.
3. `Requirement type coverage`
   - Count unique `type` values and their distribution.
   - Matters because requirements should span function, safety, ergonomics, interfaces, cost, and lifecycle concerns.
   - Direction: higher is better if the spread reflects the case.
4. `Measurability completeness`
   - Fraction of requirements with non-empty `metric`, `target`, `verification`, and `source`.
   - Matters because a requirements list should support verification and downstream design decisions.
   - Direction: higher is better.
5. `Quantified requirement ratio`
   - Fraction of requirements whose `target` contains a number, range, threshold, or explicit comparator.
   - Matters because quantified demands reduce ambiguity.
   - Direction: higher is generally better, but not every requirement must be numeric.
6. `Open-question count`
   - Count `requirements_list.open_questions`.
   - Matters because unresolved brief gaps affect later uncertainty.
   - Direction: lower is better if key assumptions are otherwise captured.
7. `Traceability coverage`
   - Count objectives and constraints referenced in `traceability`.
   - Matters because the specification should derive from the clarified task, not from arbitrary invention.
   - Direction: higher is better.

### Subjective rubric
1. `Completeness`
   - `1`: Major parts of the brief are missing.
   - `3`: Core demands are present, but some stakeholder or lifecycle needs are thin.
   - `5`: Captures the main design demands, wishes, constraints, and verification intent.
2. `Non-ambiguity`
   - `1`: Many vague or overlapping statements.
   - `3`: Some statements are still broad but mostly interpretable.
   - `5`: Statements are specific, distinct, and minimally ambiguous.
3. `Testability`
   - `1`: Most requirements cannot be verified.
   - `3`: Many are testable, some still rely on vague targets.
   - `5`: Nearly all demands are checkable by test, inspection, analysis, or demonstration.
4. `Consistency`
   - `1`: Conflicts, duplicates, or mixed abstraction levels dominate.
   - `3`: Mostly consistent, with a few overlaps or hidden assumptions.
   - `5`: Coherent set of non-duplicative, non-conflicting requirements.
5. `Problem-boundary alignment`
   - `1`: Includes solution choices or irrelevant downstream design content.
   - `3`: Mostly specification-focused, with some concept leakage.
   - `5`: Clearly defines what the tool must achieve without prematurely defining how.

### LLM evaluator
- Check whether each requirement is solution-neutral, measurable, and non-duplicative.
- Compare against `outputs/01_task_clarification_model.yaml` to confirm coverage of objectives, stakeholders, operating conditions, and exclusions.
- Penalize concepts disguised as requirements.

### Human evaluator
- Ask whether this reads like a design specification rather than a concept proposal.
- Check whether the must-have demands are sufficient to judge later concepts.
- Check whether safety, ergonomics, and cost are represented explicitly, not assumed.

## 03_black_box_model.yaml
**Stage:** Black box / overall transformation system

### Objective metrics
1. `Flow count by class`
   - Count input and output flows by `type`: material, energy, signal.
   - Matters because the black box should express the system as a transformation of flows.
   - Direction: descriptive; presence of all relevant classes is desirable.
2. `Input-output class balance`
   - Check whether each major input class has an output-side transformation or dissipation counterpart where appropriate.
   - Matters because a black box should represent transformation completeness.
   - Direction: pass/fail with notes.
3. `External interaction count`
   - Count `external_interactions`.
   - Matters because environment and operator interactions shape system boundaries.
   - Direction: higher is better only if relevant and non-redundant.
4. `Requirement traceability coverage`
   - Count distinct requirement IDs under `traceability.derived_from_requirements`.
   - Matters because the black box should emerge from the requirements list.
   - Direction: higher is better.
5. `Signal explicitness`
   - Count signal flows and whether they represent operator information or feedback.
   - Matters because manual tools often depend on human-mediated signal flows.
   - Direction: pass/fail with notes.
6. `Assumption and open-question count`
   - Count explicit assumptions and unresolved questions.
   - Matters because black-box abstraction often exposes missing flow information.
   - Direction: descriptive.

### Subjective rubric
1. `Transformation correctness`
   - `1`: Overall function is not expressed as a transformation.
   - `3`: The core transformation is present but incompletely phrased.
   - `5`: Clearly states how inputs are transformed into outputs at system level.
2. `Flow completeness`
   - `1`: Major material, energy, or signal flows are missing.
   - `3`: Core flows exist, but secondary or dissipation flows are thin.
   - `5`: Includes all important flow classes and relevant outputs/disturbances.
3. `System-environment separation`
   - `1`: Internal components appear instead of external flows.
   - `3`: Mostly abstract, with some leakage into embodiment detail.
   - `5`: Clean separation between system boundary and environment.
4. `Interaction relevance`
   - `1`: Interactions are missing or unrelated.
   - `3`: Interactions are plausible but not fully tied to requirements.
   - `5`: Interactions meaningfully represent operator and environment effects.

### LLM evaluator
- Verify that the model stays at black-box level and does not decompose into modules or mechanisms.
- Compare flows against the requirements list and the problem statement.
- Penalize missing operator input, missing workpiece flow, and premature component descriptions.

### Human evaluator
- Ask whether the artifact explains what the system transforms, not how it is built.
- Check whether the boundary includes only the designed tool and excludes manufacturing of brake components.

## 04_function_structure_model.yaml
**Stage:** Function structure / subfunction decomposition

### Objective metrics
1. `Subfunction count`
   - Count `function_structure.subfunctions`.
   - Matters because the overall function should be decomposed into actionable subfunctions.
   - Direction: descriptive; enough functions to cover the task without fragmentation.
2. `Average requirements allocated per subfunction`
   - Mean count of `allocated_requirements` per subfunction.
   - Matters because functions should be traceable to the requirement base.
   - Direction: descriptive.
3. `Connection count`
   - Count `function_structure.connections`.
   - Matters because a function structure should include a function network, not only a list.
   - Direction: higher is better up to meaningful completeness.
4. `Flow reuse consistency`
   - Fraction of output flows that reappear as downstream input flows using the same IDs or names.
   - Matters because flow conservation is central to function structures.
   - Direction: higher is better.
5. `Hierarchy depth`
   - Measure depth and completeness of `hierarchy`.
   - Matters because the decomposition should show logical structure under `F0`.
   - Direction: descriptive.
6. `Completeness-check population`
   - Check whether `completeness_checks.flow_conservation_notes` and `missing_functions_suspected` are populated meaningfully.
   - Matters because this stage should include reflection on decomposition completeness.
   - Direction: pass/fail with notes.
7. `Black-box flow coverage`
   - Fraction of black-box flows represented somewhere in subfunction inputs/outputs or connection flow IDs.
   - Matters because the function structure should refine the black box.
   - Direction: higher is better.

### Subjective rubric
1. `Decomposition quality`
   - `1`: Major functions are missing or overlapping.
   - `3`: Core functions are present but some splits are awkward.
   - `5`: Clear, coherent decomposition into meaningful subfunctions.
2. `Flow conservation`
   - `1`: Flows appear/disappear without explanation.
   - `3`: Some inconsistencies exist but the main chain is understandable.
   - `5`: Flows are consistently carried through the network.
3. `Granularity appropriateness`
   - `1`: Functions are either too broad or too fragmented to guide concept generation.
   - `3`: Mostly workable, though some levels are uneven.
   - `5`: Well-pitched level of abstraction for principle search.
4. `Avoidance of solution bias`
   - `1`: Functions are written as components or mechanisms.
   - `3`: Some wording hints at solutions.
   - `5`: Functions remain verb-noun, solution-neutral, and reusable.

### LLM evaluator
- Compare black-box flows to function inputs, outputs, hierarchy, and connections.
- Cite missing or inconsistent flows explicitly.
- Penalize solution-specific wording in `verb_noun` or descriptions.

### Human evaluator
- Ask whether another engineer could use this decomposition to search for different working principles.
- Check whether flow logic is preserved from black box to subfunctions.

## 05_working_principles_model.yaml
**Stage:** Working principles / solution principles

### Objective metrics
1. `Total principle count`
   - Count `working_principles.principles`.
   - Matters because this stage should generate alternatives before concept synthesis.
   - Direction: higher is better if the alternatives are meaningful.
2. `Principles per function`
   - Count alternatives per `function_id`.
   - Matters because key subfunctions should have genuine choice.
   - Direction: higher is better up to useful diversity.
3. `Functions with at least two alternatives`
   - Count functions with 2+ principles.
   - Matters because morphological synthesis needs alternatives, not singletons.
   - Direction: higher is better.
4. `Unique physical effects/mechanisms`
   - Count distinct `physical_effect_or_mechanism` categories or mechanisms.
   - Matters because conceptual breadth is more important than superficial renaming.
   - Direction: higher is better.
5. `Mean impacted requirements per principle`
   - Average count of `impacted_requirements`.
   - Matters because each principle should relate to design demands.
   - Direction: descriptive.
6. `Explicit evaluation ratio`
   - Fraction of principles with non-empty `advantages`, `disadvantages`, and `risks`.
   - Matters because principle evaluation should start here, not only at concept selection.
   - Direction: higher is better.

### Subjective rubric
1. `Physical plausibility`
   - `1`: Principles are vague, non-physical, or incorrect.
   - `3`: Mostly plausible, with some thin mechanism descriptions.
   - `5`: Principles are physically credible and mechanism-based.
2. `Diversity`
   - `1`: Options are mostly minor variants of the same idea.
   - `3`: Some meaningful spread exists.
   - `5`: Clear variety in physical effect or mechanism across key functions.
3. `Requirement relevance`
   - `1`: Principles are detached from requirements.
   - `3`: Requirement links exist but feel generic.
   - `5`: Links between principles and impacted requirements are specific and useful.
4. `Risk awareness`
   - `1`: Risks are absent or superficial.
   - `3`: Risks are mentioned but uneven in quality.
   - `5`: Risks are concrete and help later concept tradeoffs.

### LLM evaluator
- Use “unique working principles per subfunction” as the primary stage-defining check.
- Compare function IDs against the function structure and confirm principles are attached to the right subfunctions.
- Penalize duplicate options disguised by renamed labels.

### Human evaluator
- Ask whether the set exposes real conceptual alternatives.
- Check whether the options represent different effects or mechanisms, not merely dimensional tweaks.

## 06_concept_variants_model.yaml
**Stage:** Morphological chart and concept variants

### Objective metrics
1. `Morphological chart row count`
   - Count functions represented in `morphological_chart`.
   - Matters because key subfunctions should be selectable in concept synthesis.
   - Direction: higher is better if the selected rows are actually concept-driving.
2. `Options per row`
   - Count working-principle options for each function row.
   - Matters because chart utility depends on available alternatives.
   - Direction: higher is generally better.
3. `Concept count`
   - Count entries under `concepts`.
   - Matters because synthesis should produce multiple variants before selection.
   - Direction: higher is better up to manageable comparison.
4. `Principle diversity across concepts`
   - Measure how often concepts reuse the same principles versus recombine alternatives.
   - Matters because concepts should reflect meaningful combination diversity.
   - Direction: higher diversity is better.
5. `Requirement coverage per concept`
   - Count `satisfied`, `partially_satisfied`, and `not_satisfied` requirements for each concept.
   - Matters because concepts should be compared against the requirements list.
   - Direction: more satisfied and fewer not satisfied is better.
6. `Uncovered MUST requirements`
   - Count MUST requirements that remain in `not_satisfied` across all concepts.
   - Matters because concept generation should not ignore binding demands.
   - Direction: lower is better.
7. `Working-principle cross-check`
   - Verify that every `selected_principles` value exists in `05_working_principles_model.yaml`.
   - Matters because concept variants should only combine generated principles.
   - Direction: pass/fail.

### Subjective rubric
1. `Combinational diversity`
   - `1`: Concepts are near-duplicates.
   - `3`: Some variation exists but not across all key functions.
   - `5`: Concepts represent distinct combinations with different tradeoffs.
2. `Internal compatibility`
   - `1`: Principle combinations are mechanically inconsistent or implausible.
   - `3`: Mostly plausible with some unresolved interfaces.
   - `5`: Each concept is internally coherent as a concept-level architecture.
3. `Architectural clarity`
   - `1`: Descriptions are too vague to understand the concept.
   - `3`: Core architecture is understandable.
   - `5`: Architecture, benefits, and risks are clearly legible at concept level.
4. `Usefulness for selection`
   - `1`: Concepts do not support meaningful later comparison.
   - `3`: Selection is possible but weakly differentiated.
   - `5`: Concepts create a solid basis for weighted evaluation.

### LLM evaluator
- Check that each concept is composed only from listed working principles.
- Compare concept benefits and risks against the selected principles and requirement coverage.
- Penalize concepts that differ only cosmetically.

### Human evaluator
- Ask whether the concept set would support a real concept selection workshop.
- Check whether key tradeoffs are visible across concepts.

## 07_concept_selection_model.yaml
**Stage:** Concept selection / weighted decision

### Objective metrics
1. `Criteria count`
   - Count `criteria_weights`.
   - Matters because the decision matrix should reflect the clarified evaluation criteria.
   - Direction: descriptive.
2. `Weight normalization`
   - Sum all `weight` values and check whether the total equals 1.0 within tolerance.
   - Matters because weighted comparison requires valid normalization.
   - Direction: pass/fail.
3. `Scoring completeness`
   - Check whether every concept has a score for every criterion.
   - Matters because partial scoring undermines the matrix.
   - Direction: pass/fail.
4. `Evidence note presence`
   - Check whether each scored concept has explanatory `notes`.
   - Matters because concept selection should remain evidence-based and auditable.
   - Direction: higher is better.
5. `Selection consistency with totals`
   - Recompute weighted totals and verify that `selected_concept_id` matches the best-supported result.
   - Matters because the final choice should be traceable to the matrix.
   - Direction: pass/fail.
6. `Sensitivity analysis presence`
   - Check for non-empty `sensitivity_analysis_notes`.
   - Matters because robust concept selection should acknowledge decision sensitivity.
   - Direction: pass/fail.
7. `Prompt-discipline bias check`
   - Check whether scoring claims are supported by concept descriptions only, as instructed in the prompt.
   - Matters because this stage should not smuggle in external assumptions.
   - Direction: pass/fail with notes.

### Subjective rubric
1. `Fairness of weighting`
   - `1`: Weights are arbitrary or misaligned with the task.
   - `3`: Mostly defensible, some justification is weak.
   - `5`: Weights clearly reflect objectives, evaluation criteria, and must-have concerns.
2. `Evidence discipline`
   - `1`: Scores appear unsupported.
   - `3`: Some evidence exists but not consistently.
   - `5`: Scores are justified with explicit links to concept descriptions.
3. `Decision transparency`
   - `1`: Selection rationale is opaque.
   - `3`: Rationale is understandable but thin.
   - `5`: Selection follows clearly from the matrix and accompanying notes.
4. `Robustness`
   - `1`: Decision looks fragile and unexamined.
   - `3`: Some robustness thinking exists.
   - `5`: Sensitivity or uncertainty is acknowledged and discussed credibly.

### LLM evaluator
- Recompute the weighted totals.
- Check that the criteria align with `outputs/01_task_clarification_model.yaml` evaluation criteria.
- Penalize any use of evidence not present in the concept-variant artifact.

### Human evaluator
- Ask whether another reviewer could reproduce the same selection from the documented weights and notes.
- Check whether safety and quality are treated as genuinely important, not buried by convenience metrics.

## 08_embodiment_layouts_model.yaml
**Stage:** Embodiment design / preliminary layout

### Objective metrics
1. `Module count`
   - Count modules in `module_breakdown`.
   - Matters because embodiment should partition the selected concept into implementable subsystems.
   - Direction: descriptive.
2. `Interface count by type`
   - Count `interfaces` and group by `type`.
   - Matters because embodiment quality depends on explicit subsystem interactions.
   - Direction: higher is better if interfaces are meaningful.
3. `Key design parameter count`
   - Count `key_design_parameters`.
   - Matters because embodiment should identify governing dimensions or loads early.
   - Direction: higher is better up to relevance.
4. `Risk/mitigation count`
   - Count `risks_and_mitigations`.
   - Matters because embodiment should surface technical risks before detailed design.
   - Direction: higher is better if risks are specific.
5. `Function allocation coverage`
   - Fraction of modules with non-empty `allocated_functions`.
   - Matters because embodiment should remain linked to functional intent.
   - Direction: higher is better.
6. `Requirement linkage through KDPs`
   - Count requirements referenced by `linked_requirements` in KDPs.
   - Matters because embodiment parameters should connect back to the specification.
   - Direction: higher is better.
7. `Selected-concept consistency`
   - Check whether `traceability.selected_concept_id` matches the concept-selection artifact and whether the modules reflect that concept.
   - Matters because embodiment should develop the chosen concept, not a different one.
   - Direction: pass/fail with notes.

### Subjective rubric
1. `Modular clarity`
   - `1`: Modules are vague or arbitrary.
   - `3`: Modules are understandable but somewhat uneven.
   - `5`: Modules partition the system clearly and usefully.
2. `Interface sufficiency`
   - `1`: Important interfaces are missing.
   - `3`: Most interfaces are present, some constraints are thin.
   - `5`: Critical interfaces and their constraints are explicit and credible.
3. `Embodiment realism`
   - `1`: Layout ignores physical realities of the selected concept.
   - `3`: Mostly plausible but incomplete.
   - `5`: Reasonable preliminary embodiment with appropriate physical thinking.
4. `Appropriate maturity`
   - `1`: Either too abstract to guide design or too detailed for preliminary layout.
   - `3`: Mostly appropriate maturity.
   - `5`: Surfaces critical embodiment issues without pretending detailed design is done.

### LLM evaluator
- Compare the modules and interfaces against the selected concept and the requirements list.
- Check whether KDPs and risks target likely embodiment bottlenecks.
- Penalize unexplained module IDs or allocation gaps.

### Human evaluator
- Ask whether this is a plausible first embodiment of the selected concept.
- Check whether the layout identifies where force flows, alignment, and human interaction occur.

## 09_preliminary_sizing_model.yaml
**Stage:** Preliminary sizing / first-order engineering checks

### Objective metrics
1. `Load-case count`
   - Count `load_cases`.
   - Matters because preliminary sizing should identify governing cases explicitly.
   - Direction: higher is better if cases are distinct and relevant.
2. `Sizing-result count`
   - Count `sizing_results`.
   - Matters because embodiment KDPs should be translated into first-order sizing results.
   - Direction: higher is better up to relevance.
3. `Equation coverage`
   - Fraction of sizing results with non-empty `equation_or_model`.
   - Matters because calculations should be transparent, even if first-order.
   - Direction: higher is better.
4. `Assumed-values density`
   - Average count of entries in `assumed_values` per result.
   - Matters because preliminary sizing depends on explicit assumptions.
   - Direction: descriptive.
5. `Margin or safety-factor coverage`
   - Fraction of sizing results with non-empty `margin_or_factor_of_safety`.
   - Matters because engineering checks should indicate robustness, not only nominal sizing.
   - Direction: higher is better.
6. `Requirement linkage from load cases`
   - Count distinct requirement IDs across `load_cases.related_requirements`.
   - Matters because load cases should emerge from the specification.
   - Direction: higher is better.
7. `Embodiment parameter coverage`
   - Fraction of embodiment KDP IDs that receive a sizing result or explicit mention.
   - Matters because sizing should refine embodiment, not float independently.
   - Direction: higher is better.

### Subjective rubric
1. `Engineering adequacy`
   - `1`: Checks are superficial or irrelevant.
   - `3`: Basic first-order reasoning is present.
   - `5`: Appropriate preliminary engineering checks for this maturity level.
2. `Governing-case appropriateness`
   - `1`: Load cases do not reflect likely governing conditions.
   - `3`: Key cases are present but incomplete.
   - `5`: Governing cases are well chosen and connected to requirements.
3. `Assumption transparency`
   - `1`: Numbers appear without basis.
   - `3`: Most assumptions are explicit.
   - `5`: Assumptions and limitations are clear, making the calculations auditable.
4. `Dimensional and causal plausibility`
   - `1`: Results are physically dubious or internally inconsistent.
   - `3`: Mostly plausible, some coarse approximations.
   - `5`: First-order models are consistent and plausible for concept-level sizing.

### LLM evaluator
- Distinguish acceptable symbolic preliminary sizing from unjustified fabricated precision.
- Compare sizing parameters back to embodiment KDPs and requirements.
- Penalize exact-looking outputs that are unsupported by given assumptions.

### Human evaluator
- Ask whether the artifact provides enough first-order sizing guidance to de-risk the concept.
- Check whether assumptions and safety margins are credible for a manual riveting tool.

## 10_manufacturing_plan_model.yaml
**Stage:** Materials and manufacturing planning

### Objective metrics
1. `Module-plan count`
   - Count `module_plans`.
   - Matters because key embodiment modules should receive manufacturing attention.
   - Direction: higher is better up to core-module coverage.
2. `Material candidates per module`
   - Count candidate materials for each module.
   - Matters because material choice should remain open but reasoned.
   - Direction: descriptive.
3. `Process candidates per module`
   - Count candidate processes for each module.
   - Matters because manufacturability is process-dependent.
   - Direction: descriptive.
4. `Critical-characteristic coverage`
   - Fraction of module plans with non-empty `critical_characteristics`.
   - Matters because quality-driving features should be explicit.
   - Direction: higher is better.
5. `Risk coverage`
   - Fraction of module plans with non-empty `risks`.
   - Matters because manufacturing decisions should account for production risk.
   - Direction: higher is better.
6. `Embodiment-module coverage`
   - Fraction of key embodiment modules represented in `module_plans`.
   - Matters because manufacturing planning should reflect the embodiment architecture.
   - Direction: higher is better.
7. `Sizing-constraint consistency`
   - Check whether manufacturing choices respect critical sizing constraints or load demands from the preliminary sizing artifact.
   - Matters because material and process choices should support required performance.
   - Direction: pass/fail with notes.

### Subjective rubric
1. `Manufacturability reasoning`
   - `1`: Material and process choices are arbitrary.
   - `3`: Choices are plausible but some rationales are generic.
   - `5`: Rationales clearly connect design needs, cost, and likely production route.
2. `Material/process appropriateness`
   - `1`: Selections conflict with likely loads, tolerances, or environment.
   - `3`: Mostly reasonable with some weak matches.
   - `5`: Choices fit function, load, wear, precision, and likely volume.
3. `Attention to critical items`
   - `1`: Safety-critical or tolerance-critical parts are not highlighted.
   - `3`: Some criticality awareness exists.
   - `5`: Critical, wear-sensitive, and precision-sensitive items are clearly recognized.
4. `Production realism`
   - `1`: Ignores batch size, standard parts, or workshop supply realities.
   - `3`: Mostly plausible production assumptions.
   - `5`: Manufacturing choices align with the likely production context of a workshop tool.

### LLM evaluator
- Compare `module_id`s to the embodiment layout and check whether key loaded or precision features are covered.
- Check whether materials and processes are justified by load, wear, tolerance, cost, or environment.
- Penalize generic rationales that do not tie back to requirements or risks.

### Human evaluator
- Ask whether a manufacturing engineer could use this as a credible early planning basis.
- Check whether the document distinguishes structural, wear, and ergonomic parts appropriately.

## 11_preliminary_bom_model.yaml
**Stage:** Preliminary bill of materials

### Objective metrics
1. `BOM item count`
   - Count `preliminary_bom.items`.
   - Matters because preliminary embodiment should be expressed as concrete components.
   - Direction: descriptive.
2. `Module coverage`
   - Count distinct `module_id`s represented and compare with embodiment/manufacturing modules.
   - Matters because the BOM should reflect the architecture.
   - Direction: higher is better.
3. `Average specs per item`
   - Average count of `key_specs` per item.
   - Matters because each BOM item should carry enough specification content to be useful.
   - Direction: higher is better up to relevance.
4. `Field completeness ratio`
   - Fraction of items with non-empty `tolerance_notes`, `material`, `process`, `verification`, and `traces_to`.
   - Matters because a preliminary BOM should connect parts to fabrication and verification.
   - Direction: higher is better.
5. `Traceability density`
   - Average number of traced functions and requirements per item.
   - Matters because components should remain linked to function and specification.
   - Direction: higher is better if traceability is meaningful.
6. `Standard-vs-custom split`
   - Infer count of standard parts versus custom fabricated items where possible.
   - Matters because cost and manufacturability often depend on part standardization.
   - Direction: descriptive.
7. `Cross-artifact module consistency`
   - Check whether BOM items align with module IDs from embodiment and manufacturing plan.
   - Matters because the BOM should be the component-level elaboration of those artifacts.
   - Direction: pass/fail with notes.

### Subjective rubric
1. `Componentization completeness`
   - `1`: Key subsystems are missing or under-resolved.
   - `3`: Main parts are present, some support items are thin.
   - `5`: BOM captures the main items required to realize the embodiment.
2. `Specification usefulness`
   - `1`: Item entries are too vague to guide downstream work.
   - `3`: Basic specs are present.
   - `5`: Specs and tolerances are useful for early design control.
3. `Manufacturability readiness`
   - `1`: Items are not meaningfully tied to materials or processes.
   - `3`: Most items can be interpreted manufacturing-wise.
   - `5`: BOM supports credible transition toward detailed design and procurement.
4. `Traceability quality`
   - `1`: Traceability is sparse or tokenistic.
   - `3`: Traceability exists but unevenly.
   - `5`: Items connect clearly to functions and requirements.

### LLM evaluator
- Check whether every major module has at least one component-level representation.
- Compare fields against the manufacturing-plan and embodiment artifacts.
- Penalize orphan items with no clear module, function, or requirement ties.

### Human evaluator
- Ask whether the BOM would help structure detailed design or procurement discussion.
- Check whether tolerances and verification notes are appropriate for an early BOM, not fully detailed part drawings.

## 12_verification_plan_model.yaml
**Stage:** Verification and validation planning

### Objective metrics
1. `Verification entry count`
   - Count `verification_plan.requirement_verification`.
   - Matters because this stage should operationalize requirement checking.
   - Direction: higher is better up to full coverage of MUST requirements.
2. `MUST-requirement coverage`
   - Fraction of MUST requirements from `02_requirements_list_model.yaml` that appear in `requirement_verification`.
   - Matters because every binding requirement should have a verification activity.
   - Direction: higher is better; target is complete coverage.
3. `Method distribution`
   - Count entries by `method`: analysis, test, inspection, demonstration.
   - Matters because method choice should fit requirement type.
   - Direction: descriptive.
4. `Verification-field completeness`
   - Fraction of entries with non-empty `conditions`, `metric`, `acceptance_criteria`, and `linked_components`.
   - Matters because verifiability depends on executable procedures and thresholds.
   - Direction: higher is better.
5. `Acceptance-criteria specificity`
   - Fraction of entries whose `acceptance_criteria` contain explicit thresholds, ranges, or pass/fail conditions.
   - Matters because vague pass conditions weaken validation.
   - Direction: higher is better.
6. `Coverage-gap count`
   - Count items in `coverage_gaps`.
   - Matters because the plan should acknowledge what is not yet verifiable.
   - Direction: lower is better if justified.
7. `BOM/embodiment linkage`
   - Check whether linked components exist in the BOM or embodiment artifacts.
   - Matters because verification should tie to the actual design representation.
   - Direction: pass/fail with notes.

### Subjective rubric
1. `Testability`
   - `1`: Procedures are too vague to execute.
   - `3`: Verification intent is understandable but some procedures are thin.
   - `5`: Procedures are concrete enough for planning and later execution.
2. `Acceptance-criteria adequacy`
   - `1`: Criteria are vague or subjective.
   - `3`: Some criteria are measurable, others are broad.
   - `5`: Criteria are clear, measurable, and requirement-matched.
3. `Method-requirement fit`
   - `1`: Methods do not suit the requirement type.
   - `3`: Most methods are plausible.
   - `5`: Method choices fit the nature of the requirement consistently.
4. `End-to-end coverage quality`
   - `1`: Coverage is fragmented and misses core demands.
   - `3`: Core demands are covered, some gaps remain.
   - `5`: MUST requirements are comprehensively covered with explicit gap handling.

### LLM evaluator
- Start by checking complete coverage of MUST requirements from the requirements list.
- Compare methods and acceptance criteria against the requirement type and target.
- Penalize vague acceptance criteria, missing test conditions, and nonexistent linked components.

### Human evaluator
- Ask whether a test engineer could plan verification activities from this artifact.
- Check whether safety-critical and quality-critical requirements receive sufficiently concrete verification.

## 13_detail_design_model.yaml
**Stage:** Detail design completion / downstream deliverable stub

### Objective metrics
1. `Status explicitness`
   - Check whether `detail_design.status` is populated and unambiguous.
   - Matters because this artifact should clearly state whether the design is complete, partial, or still a stub.
   - Direction: pass/fail with notes.
2. `Placeholder coverage`
   - Check whether `placeholders.part_drawings`, `placeholders.bom`, `placeholders.manufacturing_plan`, and `placeholders.verification_plan` are all present.
   - Matters because this stage should expose what detailed deliverables exist versus what is still missing.
   - Direction: pass/fail.
3. `Expected-artifact count`
   - Count `expected_artifacts_if_extended`.
   - Matters because a stub-style detail design should name the next concrete deliverables needed for completion.
   - Direction: descriptive.
4. `Open-question count`
   - Count `detail_design.open_questions`.
   - Matters because unresolved detailed-design blockers should be explicit at this stage.
   - Direction: descriptive.
5. `Assumption count`
   - Count `detail_design.assumptions`.
   - Matters because any remaining gaps between preliminary design and detailed release should be surfaced honestly.
   - Direction: descriptive.

### Subjective rubric
1. `Completion-state honesty`
   - `1`: The artifact overclaims completion or hides major missing inputs.
   - `3`: Completion status is understandable but some gaps are understated.
   - `5`: The artifact clearly and honestly communicates readiness versus incompleteness.
2. `Consistency with prior artifacts`
   - `1`: The detail-design stub conflicts with the BOM, verification plan, or manufacturing plan.
   - `3`: Mostly aligned, with some loose or generic references.
   - `5`: Clearly consistent with the upstream design state and referenced deliverables.
3. `Usefulness as a handoff artifact`
   - `1`: The artifact gives little guidance for downstream detail work.
   - `3`: It indicates the next work but lacks structure or specificity.
   - `5`: It provides a clear, realistic handoff for detailed engineering completion.
4. `Appropriate level of detail`
   - `1`: Either empty or pretending to contain finalized detail design without supporting evidence.
   - `3`: Some useful structure exists, but depth is uneven.
   - `5`: The artifact stays faithful to stub-level completion while still being actionable.

### LLM evaluator
- Compare this artifact against `11_preliminary_bom_model.yaml`, `12_verification_plan_model.yaml`, and `10_manufacturing_plan_model.yaml`.
- Penalize placeholder sections that contradict prior artifacts or imply detailed release content that is not actually supported.
- Reward explicit naming of missing detailed-design inputs and realistic handoff framing.

### Human evaluator
- Ask whether a downstream engineer would understand what is complete and what still needs to be produced.
- Check whether the stub is responsibly limited rather than pretending the design is already released.

## Cross-File Evaluation Chain
Use the following cross-file checks to evaluate process consistency:

1. `Task clarification -> Requirements`
   - Confirm requirements faithfully formalize the clarified problem, objectives, constraints, and boundary.
2. `Requirements -> Black box`
   - Confirm the black box reflects the specified system boundary, flows, and operator/environment interactions.
3. `Black box -> Function structure`
   - Confirm black-box flows are refined into conserved subfunction flows and connections.
4. `Function structure -> Working principles`
   - Confirm each key subfunction has plausible principle alternatives.
5. `Working principles -> Concept variants`
   - Confirm concepts only combine available working principles and expose meaningful tradeoffs.
6. `Concept variants -> Concept selection`
   - Confirm weighted scoring uses the generated concepts and documented evaluation criteria only.
7. `Concept selection -> Embodiment`
   - Confirm the embodiment develops the selected concept rather than a different architecture.
8. `Embodiment -> Preliminary sizing / Manufacturing plan / BOM`
   - Confirm modules, KDPs, materials/processes, and parts align across these outputs.
9. `Requirements + BOM + Embodiment -> Verification`
   - Confirm MUST requirements are covered and linked to actual parts or subsystems.
10. `BOM + Manufacturing + Verification -> Detail design`
   - Confirm the detail-design stub accurately reflects the available upstream deliverables and missing downstream work.

## Recommended Evaluation Record Template
Use this template for each artifact:

```markdown
### <file name>
Stage:

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

Cross-file issues:
- 
```

## Validation Checklist For This Framework
- Each section names the correct `*_model.yaml` file and intended Pahl and Beitz stage.
- Prompt or packet references use artifact stage intent, not execution-local log naming.
- Every objective metric is computable from the artifact itself or direct cross-file comparison.
- Every rubric dimension evaluates stage quality, not generic writing quality.
- Cross-file checks cover the full process chain from task clarification through detail design.
