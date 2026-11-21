# Clinical OCR Benchmark

A comparative evaluation of OCR systems and vision-language models on clinical-style documents, covering printed text, handwriting, and tables. This project brings together model implementations, dataset preparation, OCR outputs, quantitative evaluation, and comparative analysis in a reproducible benchmarking workflow.

## Overview

Optical Character Recognition (OCR) is used to extract text from document images. However, model performance can vary depending on document format, handwriting, layout complexity, and the underlying architecture.

This project benchmarks multiple OCR systems on the ClinOCR dataset to study their text-extraction performance across different document types. It also documents model-specific implementation requirements, environment constraints, and practical issues encountered during evaluation.

The repository contains the dataset metadata, model catalogue, implementation notebooks, saved results, and analysis pipeline.

## Dataset

The benchmark uses the ClinOCR dataset, represented by `ClinOCR_Dataset.csv` and the corresponding indexed document images.

The dataset contains **192 documents** across three subsets:

| Subset | Number of documents |
|---|---:|
| Normal | 64 |
| Handwriting | 64 |
| Tables | 64 |
| **Total** | **192** |

The dataset CSV includes the following fields:

- `doc_id`: Unique document identifier
- `image`: Image path associated with the document
- `ground_truth`: Reference text used for evaluation
- `subset`: Document category
- `template`, `sample`, `homo_id`, and `hetero_id`: Additional dataset metadata

The reference text is compared against each model's OCR prediction to calculate evaluation metrics.

**Dataset setup:** Place `ClinOCR_Dataset.csv` and the corresponding image archive in the location expected by the notebook you intend to run. Update the dataset and image paths if using a different environment.

## Models

The project investigates OCR systems across several broad architectural categories.

### Traditional OCR systems
- Tesseract
- EasyOCR
- docTR
- PP-OCRv6

### Transformer-based document-processing systems
- Surya OCR
- Docling
- Nougat

### OCR-focused vision-language models
- DotsOCR
- olmOCR-2
- HunyuanOCR
- PaddleOCR-VL
- Chandra OCR
- Chandra OCR 2
- DeepSeek OCR
- DeepSeek OCR 2
- MinerU 2.5
- GOT-OCR 2
- Unlimited-OCR

### General-purpose vision-language models evaluated for OCR
- Florence-2
- InternVL2
- Qwen2.5-VL

The accompanying model catalogue records model versions, organizations, documentation, repositories, licenses, architectural details, benchmark information, environment requirements, and implementation notes where available.

**Evaluation coverage varies by model.** Not every model was necessarily evaluated on every subset. Refer to the saved results and analysis notebook for the actual coverage of each experiment.

## Methodology

The benchmark workflow consists of the following stages:

1. **Dataset preparation:** Load the dataset metadata and corresponding document images.
2. **Model implementation:** Configure each OCR system using its required libraries, model weights, and inference settings.
3. **Inference:** Run OCR on the selected document images and save the extracted text, execution time, and relevant output files.
4. **Text preprocessing:** Normalize whitespace and clean selected model outputs where required for evaluation.
5. **Metric calculation:** Compare predictions against reference text using a common evaluation pipeline.
6. **Comparative analysis:** Aggregate metrics, generate visualizations, and compare model performance.

### Evaluation metrics

| Metric | Interpretation | Preferred direction |
|---|---|---|
| Character Error Rate (CER) | Character-level edit distance relative to reference-text length | Lower |
| Word Error Rate (WER) | Word-level transcription error rate | Lower |
| Normalized Edit Distance (NED) | Edit distance normalized by the longer text length | Lower |
| Character Accuracy | Defined in this project as `max(0, 1 − CER)` | Higher |
| Precision | Character-level precision derived from edit operations | Higher |
| Recall | Character-level recall derived from edit operations | Higher |
| F1 Score | Harmonic mean of the calculated precision and recall | Higher |
| Runtime | Recorded inference time in seconds | Lower, subject to hardware and setup |

Text normalization replaces line breaks and tabs with spaces, collapses repeated whitespace, and strips leading and trailing whitespace before calculating the text metrics.

The precision, recall, and F1 calculations are based on character-level edit operations. These definitions are specific to this implementation and should be considered when comparing the results with other benchmarks.

## Repository Structure

The repository is organised around the dataset, model implementations, and analysis workflow.

```text
clinical-ocr-benchmark/
├── README.md
├── ClinOCR_Dataset.csv
├── OCRModel_Catalouge.csv
├── OCRModel_Implementation.ipynb
├── ocrmodel-implementation.ipynb
├── ocrmodel-implementation3.ipynb
├── OCRModels_CompleteAnalysis.ipynb
└── results/
    ├── [saved model results]
    ├── [combined evaluation datasets]
    └── [raw OCR outputs and indexed images archive]
```

The structure above describes the intended organisation; the exact filenames and contents inside `results/` should match the files committed to the repository.

## Results and Outputs

The repository includes saved benchmark results and analysis artifacts generated during the experiments.

Depending on the model implementation, result files may contain:

- Document identifiers and dataset metadata
- Ground-truth text and predicted text
- Model names and execution times
- Paths to raw OCR outputs
- Per-document evaluation metrics
- Aggregated model-level comparisons and visualizations

The analysis notebook produces model-level summaries and visualizations for CER, WER, character accuracy, F1, and runtime.

For fair comparisons, check which documents and subsets are represented in each model's results. Differences in evaluation coverage, hardware, inference settings, and output-cleaning procedures can affect the interpretation of aggregate scores.

Character Error Rate (CER) is treated as the primary evaluation metric because it measures character-level transcription errors relative to the reference text. The analysis also considers character accuracy, precision, recall, F1-score, runtime, document-level wins, document type, document length, and pairwise model comparisons.

1. Overall OCR Performance

DeepSeek-OCR-3B achieved the lowest CER, 0.128, among the results reported in this study, alongside a character accuracy of 87.2%, F1-score of 0.929, precision of 0.955, and recall of 0.910. However, DeepSeek-OCR-3B was evaluated only on the normal-document subset. On handwritten documents, inference exceeded 20 minutes per document and produced repeated output lines. Its results should therefore be interpreted as evidence of performance on standard printed documents, not as a directly comparable full-benchmark result.

Among models evaluated across the full benchmark, PP-OCRv6 achieved the lowest average CER (0.247), followed by docTR (0.251), Surya OCR (0.278), EasyOCR (0.279), and Tesseract (0.306). These results show that conventional and specialised OCR systems can outperform larger vision-language models in average transcription accuracy on this dataset.

Several VLM-based models achieved relatively high F1-scores despite higher CER values. PaddleOCR-VL-0.9B, HunyuanOCR, and Unlimited-OCR had CER values of approximately 0.440, 0.395, and 0.394, respectively. This divergence highlights that different evaluation metrics capture different aspects of transcription quality. A high F1-score does not necessarily imply a low character-level edit distance, particularly when insertions, substitutions, or formatting differences affect the predicted text.

2. Document-Level Wins and Pairwise Comparisons

Average CER and document-level wins reveal different aspects of model performance. Chandra-OCR-2 achieved the lowest CER on approximately 69 individual documents, followed by Unlimited-OCR (31), PaddleOCR-VL-0.9B (24), and HunyuanOCR (17). Despite its high number of document-level wins, Chandra-OCR-2 had an average CER of approximately 0.54, considerably higher than PP-OCRv6 and docTR.

This suggests that Chandra-OCR-2 performs exceptionally well on certain documents but has substantially larger errors on others. Unlimited-OCR shows a similar pattern, combining a relatively high number of document-level wins with an average CER of approximately 0.394.

The pairwise comparison matrix also highlights the absence of a universally dominant model. Chandra-OCR-2 performed strongly in pairwise comparisons, while PP-OCRv6, docTR, Surya OCR, and EasyOCR showed more consistent average CER results. The pairwise results and average CER should be interpreted together: a model can frequently outperform alternatives on individual documents without maintaining the lowest average transcription error across the dataset.


3. Runtime and Computational Efficiency

Runtime analysis revealed a trade-off between transcription accuracy and computational cost. Docling, EasyOCR, Surya OCR, and Tesseract were among the fastest systems, generally completing inference within a few seconds per document. docTR and Nougat were also relatively fast compared with the larger models.

In contrast, Chandra-OCR-2 and olmOCR-2 required approximately 137 and 145 seconds per document, respectively. MinerU2.5-1.2B and PP-OCRv6 required approximately 76 and 68 seconds, while DeepSeek-OCR-3B required approximately 58 seconds in the benchmarked configuration.

PP-OCRv6 and docTR therefore offer an interesting balance between average CER and inference time in this evaluation. Runtime measurements remain dependent on hardware, model configuration, implementation, and the environment in which inference was performed.


4. Performance Across Document Types

Handwritten documents were consistently the most challenging subset for nearly every model. The difference was particularly pronounced for Chandra-OCR-2 and GOT-OCR2, which had handwriting CER values of approximately 1.52 and 0.92, respectively.

Table-rich documents also presented challenges for several models, including DeepSeek-OCR-2, DotsOCR, MinerU2.5-1.2B, and Chandra-OCR-2. Some conventional OCR systems performed comparatively well on these documents.

PaddleOCR-VL-0.9B, HunyuanOCR, Unlimited-OCR, and olmOCR-2 also showed substantial variation across document types. Their overall CER was therefore influenced by the mixture of normal, handwritten, and table documents in the benchmark. Normal printed documents generally produced lower error rates, consistent with DeepSeek-OCR-3B's strong performance on that subset.

5. Effect of Document Length

Average CER showed a gradual increase with document length. The shortest two document-length groups had average CER values of approximately 0.209 and 0.210, followed by 0.267, 0.294, and 0.311 across progressively longer groups.

This indicates a weak but increasing relationship between document length and transcription error in the evaluated dataset. Longer documents provide more opportunities for recognition errors, although the substantially larger differences across document types suggest that handwriting, layout, and document complexity are also important factors.


6. Implementation and Benchmarking Challenges

An important component of this project was establishing a common evaluation workflow for OCR systems originating from different repositories and frameworks. Models required different software environments, dependencies, input formats, and inference pipelines. Implementation also involved troubleshooting package incompatibilities, CUDA and hardware requirements, API changes, and incomplete documentation.

Preprocessing, inference, metric calculation, runtime measurement, and result aggregation were standardised as far as practical to enable systematic comparison. The resulting workflow brings together model-specific implementations and saved predictions with common evaluation and analysis code.

## Reproducibility and Limitations

This project aims to make the benchmarking workflow inspectable and reproducible by providing the dataset metadata, model catalogue, implementation notebooks, saved outputs, and evaluation code.

Several limitations should be considered:

- **Model coverage:** Some models may have results for only a subset of the benchmark.
- **Environment differences:** OCR systems can require incompatible package versions, model-specific dependencies, or custom configuration.
- **Hardware dependence:** Runtime measurements depend on the hardware and execution environment used.
- **Metric limitations:** CER and WER measure transcription similarity, but do not fully capture reading order, spatial layout, table structure, or semantic correctness.
- **Preprocessing:** Text normalization and model-specific output cleaning can influence evaluation scores.
- **Dataset scope:** Results on this dataset should not automatically be interpreted as evidence of performance on real-world clinical records or other unseen document distributions.

Saved result files allow the analysis to be inspected without rerunning every model. Reproducing inference from scratch may require additional compute resources, model downloads, access permissions, and environment configuration.

## Model Catalogue

The model catalogue is provided as a reference for the OCR systems investigated in this project. It includes links to model repositories and documentation, version information, architectural descriptions, environment notes and implementation observations where available.

## Acknowledgements

This work builds on the publicly available OCR systems, model repositories, libraries, and documentation cited in the accompanying catalogue and notebooks. Please consult the relevant upstream resources for each model's licence and usage conditions.
