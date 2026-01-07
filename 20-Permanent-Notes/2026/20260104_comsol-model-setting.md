---
id: 202601041709-comsol-model-setting
created: 2026-01-04
updated: 2026-01-04
tags:
  - permanent
  - "{ domain }":
  - "{ concept_type }":
type: permanent-note
status: draft
confidence: medium
source_type:
  "{ source_type }":
---

# Key Points

- COMSOLでは計算モデルを設定できる
- {{point2}}
- {{point3}}

# Context
{{why_this_matters}}

# Body

model.common()
.The common list contains nodes that have different purposes depending on the node type. The type is assigned when the node is created. All other properties are set and accessed using generic set and get methods as described under set() and get* and Selection Access Methods. The different types may or may not use a selection. The following node types are currently defined:

| TYPE                                                      | PURPOSE             | DESCRIPTION                                                                             |
| :-------------------------------------------------------- | ------------------- | --------------------------------------------------------------------------------------- |
| Matrix                                                    | Variable utility    | Define a matrix of variables.<br>Uses a selection.                                      |
| MatrixInverse                                             | Variable utility    | Compute the inverse of a matrix.<br>Uses a selection.                                   |
| MatrixDiagonalization                                     | Variable utility    | Diagonalize a symmetric 3-by-3 matrix.<br>Uses a selection.                             |
| MatrixDecomposition                                       | Variable utility    | Use SVD to decompose a matrix.<br>Uses a selection.                                     |
| ParticipationFactors                                      | Physics variables   | Set up participation factor evaluation.                                                 |
| ResponseSpectrum                                          | Physics variables   | Set up response spectrum evaluation.                                                    |
| AmbientThermalProperties                                  | Physics variables   | Set up ambient thermal conditions using meteorological data.                            |
| GlobalReducedModelInputs                                  | Control variables   | Define global control variables for use as inputs to model reduction                    |
| DensityTopology [[20260104_comsol-topology-optimization]] | Control variables   | Define a control variable field and a filtered density field for topology optimization. |
| CommonInputDefault                                        | Common model inputs | Set default values of input quantities required by materials.                           |
| CommonInputDef                                            | Common model inputs | Override values of input quantities on selected entities.                               |
| PrescribedDeformation                                     | Moving mesh         | Prescribe a mesh deformation on domains. Uses a selection.                              |
| RotatingDomain                                            | Moving mesh         | Prescribe rotation of a domain. Uses a selection.                                       |
| FixedBoundary                                             | Moving mesh         | Prescribe zero displacement of a deforming domain boundary. Uses a selection.           |
| PrescribedMeshDisplacement                                | Moving mesh         | Prescribe displacement of a deforming domain boundary. Uses a selection.                |
| PrescribedNormalMeshDisplacement                          | Moving mesh         | Prescribe a normal displacement of a deforming domain boundary. Uses a selection.       |
| PrescribedNormalMeshVelocity                              | Moving mesh         | Prescribe the normal velocity of a deforming domain boundary. Uses a selection.         |
| Slip                                                      | Moving mesh         | Prescribe mesh slip behavior of a deforming domain boundary. Uses a selection.          |
| Symmetry                                                  | Moving mesh         | Prescribe symmetry on a deforming domain boundary. Uses a selection.                    |
| DeformingDomain                                           | Moving mesh         | Specify free deformation of a domain. Uses a selection.                                 |

## Related Concepts

関係する概念とその関係性について1行で簡潔に記載する

- {{related1}} - {{relationship_type}}
    - {{description1}}
- {{related2}} - {{relationship_type}}
    - {{description2}}

## Sources
- {{Literature_note1}}
- {{Literature_note2}}
- {{WEB_link1}}

<!-- 以下はオプション -->

# 応用例

{{application_examples}}

---
- Generated: {{generation_timestamp}}
- Model: {{ai_model_used}}