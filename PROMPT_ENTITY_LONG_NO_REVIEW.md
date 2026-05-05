This prompt is written in Markdown format.

__Process description__:
- Objective: identify an investment population related to a theme definition using the FACTSET RBICS Revenue dataset.
- The process has two steps:

1) **Step 1 (already completed by SMEs):** Map RBICS Revenue taxonomy to one category.
    - **Core**: RBICS revenue category name and description are likely to be mainly theme-related activities. Fully identified by SMEs.
    - **Eligible Under Condition (EUC)**: RBICS revenue category name and description are not sufficient to conclude thematic relevance without additional information (e.g., business/product descriptions, company reports, or other company-specific evidence). SME validation is best-effort and may not be complete.
    - **Loosely related**: Activity may be indirectly related to the theme, but is **not directly related** to the theme definition.
    - **Unrelated**: Activity is not likely to be related to the theme, even when considering additional data points.

2) **Step 2 (current process):** Allocate revenue for each entity using Step 1 categorization and entity-level information such as company description and business segment names.

**Scope for this prompt:** You are executing **Step 2, Level 1, ENTITY-ONLY assessment**. You are scoring how well the theme definition aligns to the entity business description, considered in complete isolation from any business segment, RBICS taxonomy, or SME categorization. References to business segment level, Level 2 (RBICS L6 reassessment), and Level 3 (Entity-level reassessment) are not tasks in this prompt.

__FACTSET Revenue dataset - conceptual definitions__:
The following definitions apply for the purpose of this ask.

| Hierarchy Position | Concept | Description |
|--------------------|---------|-------------|
| 1 | entity | Corresponds to a public or private company and has an associated description of its business. |
| 2 | Financial report | Corresponds to a financial report of the entity for a given period (usually annual reports covering 12 months). |
| 3 | Level 6 (L6) RBICS revenue hierarchy | Corresponds to a standardized revenue taxonomy maintained by FACTSET. Can be associated to one or more business segments of a given entity. |
| 4 | Business segment | Corresponds to a revenue entry in a given financial report. |

The hierarchy position represents the level of aggregation, with entity being the most aggregate concept and business segment being the most granular concept.

__Prior information (not subject to change)__:
- Company description is provided by a third-party supplier and should be the main source of information about a particular company. You should avoid making assumptions about this company based on wider external knowledge.
- These SME priors are anchors for tie-breaking; they do NOT override clear entity-level evidence.

__STEP 2 - LEVEL 1 ENTITY-ONLY (WHAT IS REQUIRED OF YOU)__:
You are acting as an expert investment analyst for this theme.
Use expertise only to interpret the entity business description against the theme definition.
Do NOT use external or web knowledge; use only the provided inputs.

Remember: you are assessing ONE entity at Step 2, Level 1, ENTITY-ONLY. No business segment, no taxonomy, no SME label is in scope for this prompt.

**Method (do all)**:

1) **Entity Match Score (ENT_MATCH)**: Provide a score 0 to 100 reflecting how well the theme definition aligns to the entity business description (score of 100 being perfect alignment). ONLY consider the theme definition and the entity description in isolation.

    **Scoring rules anchored to this theme definition**:

    - The theme primary focus is on local and/or national Defence applications. Companies whose described activities serve defence, national security, or military sectors directly are in-theme.
    - Dual-use is in scope ONLY where the entity description indicates significant defence/military application of the dual-use technology. A dual-use product with no described defence application is NOT in-theme on that basis alone.
    - Upstream supply-chain cases (e.g., generic raw materials, commodity components) are NOT in-theme unless the entity description specifies the product is purpose-built for defence end-use.
    - In-theme activities include any of the following sub-domains as named in the theme definition: Advanced Weaponry and Munitions; Aerospace Systems (military aircraft, UAVs, space launch); Land-based Systems (armored/unmanned ground vehicles); Naval Systems (military ships, unmanned water vehicles); Defence Electronics and Sensors (radar, avionics, defence processors, defence networking); Defence Tech (AI, IoT, robotics, analytics for defence applications, government IT, network security for critical defence infrastructure); Missile Defense; Cybersecurity for military networks; Surveillance and Reconnaissance (ISR); Command, Control, and Communications (C5ISR/C6ISR); Smart Borders; Military-grade Wearables and Robotics; Technical Training and Simulation for military systems; Network Security and Resilience for defence-related networks; Government IT and Public Sector Software for defence applications.

    **Score band rubric (use this to anchor the score)**:

    | Band | Range | Definition | Defence-specific examples |
    |------|-------|------------|---------------------------|
    | Unrelated | 0-25 | Entity description shows no meaningful overlap with any in-theme sub-domain. No defence, military, national security, or government-defence customer is named or implied. | Pure consumer goods, retail, hospitality, food production, asset management with no defence exposure. |
    | Tangential | 26-50 | Entity description shows possible or indirect overlap. Either: (a) describes dual-use technology without naming defence/military application, or (b) describes upstream/commodity products that could conceivably be sold into defence but no defence customer is named, or (c) names defence only as one minor end-market among many. | Generic semiconductor company; commercial cybersecurity firm without named military/government defence customers; aerospace firm focused on civil aviation with brief mention of "also serving defence"; industrial materials supplier listing many end-markets including defence. |
    | Partial alignment | 51-75 | Entity description clearly indicates in-theme activities form a meaningful portion of the business but not the dominant identity. Description names specific in-theme sub-domains, products, or military/government-defence customers among other businesses. | Diversified industrial with a stated "Defence & Aerospace" division alongside other divisions; technology firm with explicit military communications product line; cybersecurity firm naming defence and government customers as a stated focus area among others. |
    | Core alignment | 76-100 | Entity description indicates in-theme activities are central to the business identity. Description prominently features in-theme sub-domains, products, or military/government-defence customers as the primary or near-exclusive business. | Prime defence contractor; military aircraft / UAV / weapons systems manufacturer; ISR specialist; missile defence company; military shipbuilder; firm explicitly self-describing as a defence, national security, or military technology company. |

    Within a band, place the score based on how strongly the description supports that band's definition. For example, an entity self-describing as "a leading defence prime" with multiple named in-theme sub-domains scores 90+. An entity with one named defence division among five non-defence divisions scores 55-65 depending on how prominently defence is described.

2) **Reasoning method**:
    a. Restate the theme definition in your own analytic terms (one short sentence).
    b. Extract from the entity description the specific phrases, products, customers, or markets relevant to assessing theme alignment. Quote them.
    c. Map each extracted element to one of the in-theme sub-domains listed above, OR mark it as out-of-theme / dual-use without defence application / upstream supply-chain.
    d. Assign a band based on the rubric above.
    e. Assign a numeric score within the band.

3) **Confidence outputs**:

    Provide TWO confidence values. They measure different things and must not be conflated.

    - **Gating by input completeness**: If `inputCompleteness` (segments_input_confidence_LEVEL1) = 0, return both `score_confidence` and `descriptionSpecificity` as 0. If = 100, compute and return both per the rubrics below.

    **3a) `score_confidence` ∈ [0, 1]** — confidence in the assigned ENT_MATCH score itself.

    This is the model's certainty that, given the same inputs again, the same score should be returned. It is NOT a measure of how detailed the entity description is. A vague description can still yield a high-confidence score (e.g., a description with no defence-related content at all confidently maps to Unrelated). A detailed description can yield a low-confidence score if the evidence within it is mixed or sits near a band boundary.

    Confidence rubric:

    | Range | Meaning |
    |-------|---------|
    | 0.85 - 1.00 | High. Evidence is unambiguous, band placement is clearly correct, within-band score is well-supported. No conflicting signals. |
    | 0.60 - 0.84 | Moderate. Band placement is supported, but within-band score involves some judgement, or minor secondary signals exist that don't change the band. |
    | 0.30 - 0.59 | Low. Band placement is plausible but a neighbouring band could also be defended. Evidence is mixed, partial, or requires interpretation. |
    | 0.00 - 0.29 | Very low. Description is too sparse, too generic, or contains contradictory signals such that the score is essentially a best-guess. |

    Drivers to consider:
    - Strength and unambiguity of theme-aligned evidence (named explicitly vs implied).
    - Band proximity: is the entity clearly inside one band, or near a boundary?
    - Conflicting signals: does the description contain both in-theme and out-of-theme elements pulling in different directions?
    - Sufficiency of evidence for THIS scoring decision (not detail in general).

    **3b) `descriptionSpecificity` ∈ [0, 1]** — input-quality measure: how specifically the entity description supports the thematic interpretation.

    Generic and vague entity descriptions which do not allow for assessment of alignment to theme have a lower score. Where entity description provides significant detail of types of products, customers, and markets, the score should be higher. This measures the description itself, independent of what score was assigned.

    Specificity rubric:

    | Range | Meaning |
    |-------|---------|
    | 0.85 - 1.00 | Description names specific products, customers, end-markets, technologies, or sub-domains in concrete terms. |
    | 0.60 - 0.84 | Description includes concrete details on most of: products, customers, markets — but some elements are general. |
    | 0.30 - 0.59 | Description is partly specific (e.g., names a sector but not products or customers) or relies on broad category labels. |
    | 0.00 - 0.29 | Description is generic boilerplate ("a diversified holding company"), lacks named products or customers, or is too short to support thematic assessment. |

    Note: `score_confidence` and `descriptionSpecificity` can diverge. Examples:
    - Vague description, clearly out-of-theme (e.g., "a regional bakery"): low `descriptionSpecificity`, high `score_confidence`.
    - Detailed description with mixed signals (e.g., named civil aviation business plus brief defence mention): high `descriptionSpecificity`, low-to-moderate `score_confidence`.

4) **Determinism requirement**:
    - Do not use free-form intuition outside these criteria for computing the score or either confidence value.
    - All three values must be reproducible: given the same theme definition and entity description, the same outputs must be returned.
    - Anchor numeric scores to the band definitions above. Do not assign scores that contradict the band definition.

__INPUTS (WHAT YOU WILL CONSIDER)__:

**Theme**: {theme}

**Theme definition**:
{theme_definition}

**Entity description**: {issuer_description}

**Input Completeness (segments_input_confidence_LEVEL1) for computing confidence outputs**: {segments_input_confidence_LEVEL1}

__OUTPUTS and respective definition (what you must provide)__:

**Rationale**:
Provide rationales for your decision, clearly articulating which input elements were most relevant in your decision-making process.
- Provide `rationale_ENT_MATCH`: must reference specific phrases from the entity description that drove the score, and name the in-theme sub-domain(s) those phrases map to (or state explicitly if none).
- Provide `rationale_score_confidence`: must explain why score confidence is at the level given — referencing strength of evidence, proximity to band boundaries, or conflicting signals.
- Provide `rationale_descriptionSpecificity`: must explain why the description is or is not specific enough — referencing what is named vs what is generic.

**Output format (single JSON object, no extra text)**:
Return exactly one valid JSON object matching the schema below.
Do not include markdown, code fences, commentary, or any extra keys.

```json
{
  "type": "object",
  "properties": {
    "ENT_MATCH": { "type": "number", "minimum": 0, "maximum": 100 },
    "ENT_MATCH_band": { "type": "string", "enum": ["Unrelated", "Tangential", "Partial alignment", "Core alignment"] },
    "score_confidence": { "type": "number", "minimum": 0, "maximum": 1 },
    "descriptionSpecificity": { "type": "number", "minimum": 0, "maximum": 1 },
    "rationale_ENT_MATCH": { "type": "string" },
    "rationale_score_confidence": { "type": "string" },
    "rationale_descriptionSpecificity": { "type": "string" }
  },
  "required": [
    "ENT_MATCH",
    "ENT_MATCH_band",
    "score_confidence",
    "descriptionSpecificity",
    "rationale_ENT_MATCH",
    "rationale_score_confidence",
    "rationale_descriptionSpecificity"
  ],
  "additionalProperties": false
}
```
