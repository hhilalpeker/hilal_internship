# Comparative Evaluation of Cell2location and RCTD in Reclassifying Unknown Spots in Spatial Transcriptomics Data 

This work was carried out at the [Daub Lab](https://www.daublab.org/) between **2 June 2025** and **2 August 2025**.  

This project aims to provide a clear comparison between **Cell2location** and **RCTD** for the reclassification of “unknown” spots in spatial transcriptomics (ST) datasets. The overall objective is to determine which method more accurately assigns cell types to ambiguous or previously unclassified regions, based on a reference single-cell RNA sequencing (scRNA-seq) dataset and pathologist annotations.

The “cell2location” open-source tool used for ST-scRNAseq integration can be found here: https://github.com/BayraktarLab/cell2location/

The “RCTD” open-source tool used for ST-scRNAseq integration can be found here: https://github.com/dmcable/spacexr/tree/master

RCTD Tutorial : https://www.bioconductor.org/packages/release/bioc/vignettes/spacexr/inst/doc/rctd-tutorial.html


---
# Main Framework of the Project
## Data and Sample Selection

### scRNA-seq Reference Dataset
- **Source**: The scRNAseq data used in this project is an open source library coming from the paper: Wu, S.Z., Al-Eryani, G., Roden, D.L. et al. A single-cell and spatially resolved atlas of human breast cancers. Nat Genet 53, 1334–1347 (2021). https://doi.org/10.1038/s41588-021-00911-1

- **Inclusion criteria**:
  - Only untreated patients
  - Only ER+ patients
  - Patients with ILC or IDC subtypes
- **Preprocessing**:
  - Mitochondrial and ribosomal genes removed

### Spatial Transcriptomics Dataset
- **Samples**: Among 48 slides from 11 patients, the slide **V10F03-034_C** was selected for model training
- **Subtypes present**: ILC, IDC, or DCIS

### Data Preprocessing & Quality Control
Both scRNA-seq and ST data were preprocessed using standard filtering criteria, including quality control steps for removing low-quality genes and cells.

---

## Cell2location Tool Training
- Implemented using Python packages
- Multiple model versions were trained to test combinations of:
  - **Cell granularity**: major types (9) vs. subtypes (48)
  - **Assignment strategy**: percentage-based vs. absolute thresholding

### Model Versions
| Version | Cell Type Granularity | Assignment Threshold | Notes |
|--------|-------------------------|------------------------|-------|
| 2.0 | Subtypes | 30% (percentage) | Overly complex, difficult evaluation |
| 2.1 | Major types | 30% (percentage) | Still affected by low estimations |
| 3.0 | Subtypes | ≥3 cells (absolute) | Biologically unfeasible clusterings |
| **3.1** | **Major types** | **≥3 cells (absolute)** | Best trade-off between interpretability and performance |

- **Version 3.1** was selected for further analysis due to:
- Simplified classification compatible with pathologist annotations (only 6 major classes)
- Avoiding over-assignment in low-expression cell types

---

## RCTD Pipeline

- Implemented using R packages
- Directly used **Version 3.1 equivalent** parameters
- The same spatial sample (**V10F03-034_C**) was used for comparability

---

## Solving the Multiple Assignment Problem

To handle cases where multiple cell types were assigned to the same spot, three strategies were tested:

1. **Maximum Cell Type**
   - The highest estimated cell type per spot was selected
   - Provided the most promising results
2. **Top Three Cell Types**
   - Top three estimations were clustered and treated as composite categories
   - Lacked biological coherence in most cases
3. **Dominant Cell Type**
   - Threshold-based filtering among top three types (≥60% for two, ≥40% for three)
   - Reduced false positives but increased "no assignment" cases

**Method 1: Maximum Cell Type** was used in both pipelines as the final assignment strategy.

---

## Evaluation Criteria

- Comparison was made based on **pathologist-annotated regions** of the tissue slides
- Metrics computed included:
  - **Accuracy**
  - **Precision**
  - **Recall**
  - **F1-score**
- Evaluations were based on counting **True Positives (TP)**, **False Positives (FP)**, **False Negatives (FN)**, and **True Negatives (TN)** for each annotation region

---
## Conclusion

This project presents a comparative workflow for evaluating **Cell2location** and **RCTD** in the context of spatial transcriptomics data deconvolution.  
By training both methods on the same reference (scRNA-seq) and spatial sample (V10F03-034_C), and applying the same cell assignment strategy (Version 3.1 + **maximum cell type** selection), a fair comparison was achieved.

**Key conclusion:**
- While both methods produced biologically meaningful results,
- **Cell2location (Version 3.1)** paired with **maximum cell type** assignment aligned more closely with **pathologist annotations** in terms of spatial structure and classification accuracy.
- However, neither method achieved perfect reclassification performance, indicating the complexity of the unknown spots and the limitations of current deconvolution approaches.

This comparison provides useful insights into the strengths and weaknesses of both pipelines when applied to breast cancer ST data, and offers a potential baseline for further method development.

---
## References

- Wu, S. Z. et al. (2021). *A single-cell and spatially resolved atlas of human breast cancers*. **Nature Genetics**.  
- Kleshchevnikov, V. et al. (2022). *Cell2location maps fine-grained cell types in spatial transcriptomics*. **Nature Biotechnology**.  
- Cable, D. M. et al. (2022). *Robust decomposition of cell type mixtures in spatial transcriptomics*. **Nature Biotechnology**.  

---

## Contact

For questions, suggestions feel free to contact me:  
[hhilalpeker@gmail.com](mailto:hhilalpeker@gmail.com)

I would like to express my sincere gratitude to the members of the CDA who supported me during this work, for their valuable guidance, feedback, and encouragement throughout the process.

## Repository Structure

```bash
cell2location/
├── version_2_0.ipynb
├── version_2_1.ipynb
├── version_3_0.ipynb
├── version_3_1.ipynb 

rctd/
├── rctd_pipelin.ipynb
├── convert.ipynb

evaluation/
├── evaluation.ipynb

scrnaseq/
├── scrnaseq_qc.ipynb
├── scrnaseq_exp_filtered.ipynb
