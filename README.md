# Zero-shot variant effect prediction with ESM-2

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/tanvigambhir/esm-variant-effects/blob/main/esm2_zero_shot_dms.ipynb)

**Question.** A protein language model trained only on natural sequences has learned which amino acids "fit" at each position. Can it predict, with no lab data and no training, how single substitutions change a protein's function? Does a bigger model predict better?

## Approach

| Step | Details |
|---|---|
| Data | [ProteinGym](https://proteingym.org) DMS assay `BLAT_ECOLX_Stiffler_2015`: TEM-1 β-lactamase (286 aa), ampicillin-resistance fitness for ~5,000 single mutants |
| Models | ESM-2 at 35M, 150M and 650M parameters (Hugging Face) |
| Score | Masked marginal: mask position *i*, then `log p(mutant) − log p(wild type)`. One pass per position yields the full L × 20 saturation map |
| Metric | Spearman ρ against measured fitness (ProteinGym standard), with 1,000-sample bootstrap 95% CIs |
| Error analysis | Accuracy by wild-type and mutant residue, position-level vs. within-position accuracy, positions and mutations with the largest rank disagreement |

## Results

<!-- Fill in from results/summary.json after running the notebook -->

| ESM-2 size | Spearman ρ | 95% CI |
|---|---|---|
| 35M | X.XX | X.XX–X.XX |
| 150M | X.XX | X.XX–X.XX |
| 650M | X.XX | X.XX–X.XX |

![Model size and scatter](figures/model_size_and_scatter.png)

![Measured vs. predicted saturation maps](figures/saturation_maps.png)

## Where the model fails

<!-- 2–4 sentences from the error-analysis cells. Prompts:
- Is position-level ρ (which sites are sensitive) much higher than median within-position ρ (which substitution is worst at a site)?
- Which wild-type / mutant residues drag accuracy down (see figures/error_by_residue.png)?
- What are the top positions the model calls tolerant but the assay calls sensitive? Look them up in the TEM-1 structure: active site (S70, K73, S130, E166), Ω-loop, buried core? -->

![Per-residue accuracy](figures/error_by_residue.png)

![Per-position tolerance](figures/position_tolerance.png)

## Reproduce

Open the notebook in Colab, set **Runtime → T4 GPU**, and run all cells (~5–10 min). To try another protein, change `DMS_ID` to any ID in ProteinGym's `DMS_substitutions.csv`.

```
esm2_zero_shot_dms.ipynb   # full pipeline
results/summary.json       # headline numbers
results/*_scores.csv       # per-mutant scores from every model
figures/                   # all plots
```

## Next steps

- Add a supervised baseline (ridge regression on ESM-2 embeddings, cross-validated by position) to measure how much a few labels add over zero-shot.
- Annotate positions with relative solvent accessibility from the AlphaFold structure to test the buried vs. surface hypothesis directly.
- Extend across many ProteinGym assays to see whether the size trend holds generally.

## References

- Lin et al. (2023). Evolutionary-scale prediction of atomic-level protein structure with a language model. *Science*. (ESM-2)
- Meier et al. (2021). Language models enable zero-shot prediction of the effects of mutations on protein function. *NeurIPS*. (masked-marginal scoring)
- Notin et al. (2023). ProteinGym: Large-scale benchmarks for protein fitness prediction and design. *NeurIPS*.
- Stiffler et al. (2015). Evolvability as a function of purifying selection in TEM-1 β-lactamase. *Cell*.
