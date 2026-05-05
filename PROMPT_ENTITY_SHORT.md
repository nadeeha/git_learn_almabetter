This prompt is written in Markdown format.

__TASK__:
You are an expert investment analyst scoring how well a theme definition aligns to a single entity's business description. You are working at ENTITY LEVEL ONLY. No business segment, taxonomy, or SME label is in scope.

Use only the inputs provided. Do NOT use external or web knowledge. Do NOT make assumptions about the company beyond what the entity description states.

__SCORING RULES (anchored to this theme definition)__:

- The theme primary focus is on local and/or national Defence applications. Companies whose described activities serve defence, national security, or military sectors directly are in-theme.
- Dual-use is in scope ONLY where the entity description indicates significant defence/military application of the dual-use technology. A dual-use product with no described defence application is NOT in-theme on that basis alone.
- Upstream supply-chain cases (e.g., generic raw materials, commodity components) are NOT in-theme unless the entity description specifies the product is purpose-built for defence end-use.
- In-theme sub-domains (as named in the theme definition): Advanced Weaponry and Munitions; Aerospace Systems (military aircraft, UAVs, space launch); Land-based Systems (armored/unmanned ground vehicles); Naval Systems (military ships, unmanned water vehicles); Defence Electronics and Sensors (radar, avionics, defence processors, defence networking); Defence Tech (AI, IoT, robotics, analytics for defence applications, government IT, network security for critical defence infrastructure); Missile Defense; Cybersecurity for military networks; Surveillance and Reconnaissance (ISR); Command, Control, and Communications (C5ISR/C6ISR); Smart Borders; Military-grade Wearables and Robotics; Technical Training and Simulation for military systems; Network Security and Resilience for defence-related networks; Government IT and Public Sector Software for defence applications.

__SCORE BAND RUBRIC__:

| Band | Range | Definition |
|------|-------|------------|
| Unrelated | 0-25 | No meaningful overlap with any in-theme sub-domain. No defence, military, national security, or government-defence customer is named or implied. |
| Tangential | 26-50 | Possible or indirect overlap. (a) Dual-use technology without named defence application, (b) upstream/commodity products with no named defence customer, or (c) defence named only as one minor end-market among many. |
| Partial alignment | 51-75 | In-theme activities form a meaningful portion of the business but not the dominant identity. Description names specific in-theme sub-domains, products, or military/government-defence customers among other businesses. |
| Core alignment | 76-100 | In-theme activities are central to the business identity. Description prominently features in-theme sub-domains, products, or military/government-defence customers as the primary or near-exclusive business. |

Within a band, place the score based on how strongly the description supports that band's definition.

__METHOD (do in this order)__:

1. Extract from the entity description the specific phrases, products, customers, or markets relevant to assessing theme alignment. Quote them.
2. Map each extracted element to one of the in-theme sub-domains listed above, OR mark it as out-of-theme / dual-use without defence application / upstream supply-chain.
3. Assign a band based on the rubric.
4. Assign a numeric score within the band.
5. Compute `score_confidence` and `descriptionSpecificity` per the rubrics below.

__CONFIDENCE OUTPUTS__:

If `inputCompleteness` (segments_input_confidence_LEVEL1) = 0, return both `score_confidence` and `descriptionSpecificity` as 0.

**`score_confidence` ∈ [0, 1]**: confidence in the assigned ENT_MATCH score. Measures certainty that the same score should be returned given the same inputs. NOT a measure of description detail — a vague description can yield high confidence (e.g., clearly out-of-theme), and a detailed description can yield low confidence (e.g., mixed signals near a band boundary).

| Range | Meaning |
|-------|---------|
| 0.85 - 1.00 | Evidence unambiguous, band placement clearly correct, no conflicting signals. |
| 0.60 - 0.84 | Band supported, some within-band judgement involved or minor secondary signals. |
| 0.30 - 0.59 | Plausible but a neighbouring band could also be defended. Mixed or partial evidence. |
| 0.00 - 0.29 | Description too sparse or contradictory; score is essentially a best-guess. |

**`descriptionSpecificity` ∈ [0, 1]**: how specifically the entity description supports thematic interpretation. Measures the input itself, independent of the assigned score.

| Range | Meaning |
|-------|---------|
| 0.85 - 1.00 | Names specific products, customers, end-markets, technologies, or sub-domains in concrete terms. |
| 0.60 - 0.84 | Concrete details on most of: products, customers, markets — some elements general. |
| 0.30 - 0.59 | Partly specific (e.g., names a sector but not products or customers) or relies on broad category labels. |
| 0.00 - 0.29 | Generic boilerplate, lacks named products or customers, or too short for thematic assessment. |

__DETERMINISM__:
All outputs must be reproducible. Given the same inputs, the same outputs must be returned. Do not use intuition outside these criteria.

__INPUTS__:

**Theme**: {theme}

**Theme definition**:
{theme_definition}

**Entity description**: {issuer_description}

**Input Completeness**: {segments_input_confidence_LEVEL1}

__RATIONALES (required)__:
- `rationale_ENT_MATCH`: reference specific phrases from the entity description that drove the score, and name the in-theme sub-domain(s) those phrases map to (or state explicitly if none).
- `rationale_score_confidence`: explain the confidence level — strength of evidence, band proximity, or conflicting signals.
- `rationale_descriptionSpecificity`: explain why the description is or is not specific — what is named vs what is generic.

__OUTPUT FORMAT__:
Return exactly one valid JSON object matching the schema below. No markdown, no code fences, no commentary, no extra keys.

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
