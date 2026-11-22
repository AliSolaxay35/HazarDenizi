# Bacterial Colony Enumeration

An Experimental Computer Vision Project For Automated Bacterial Colony Detection And Enumeration Using Classical OpenCV And Deep Learning.

## Overview

Bacterial Colony Enumeration Is An Important Task In Microbiology Laboratories, Commonly Used To Estimate The Number Of Viable Microorganisms In A Sample.

This Project Explores Automated Colony Detection And Counting From Agar Plate Images Using Two Different Computer Vision Approaches:

- **Classical Computer Vision:** Image Processing Techniques Implemented With OpenCV.
- **Deep Learning:** Zero-Shot Instance Segmentation Using FastSAM.

The Main Goal Is To Explore, Implement, And Compare These Approaches Under The Same Dataset Conditions, Highlighting Their Capabilities, Differences, And Practical Limitations.

## Project Objectives

- Detect Bacterial Colonies In Agar Plate Images.
- Estimate Colony Counts Through Automated Image Analysis.
- Implement A Classical Image Processing Pipeline Using OpenCV.
- Explore Deep Learning-Based Segmentation With FastSAM.
- Compare The Results Of Classical And Deep Learning Approaches.
- Examine Performance On Different Culture Media And Negative Control Images.
- Explore The Potential Of Computer Vision In Microbiology Workflows.

## Dataset

This Project Uses The Bacterial Colony Enumeration Dataset Available Through Zenodo.

**Dataset Information:**

| Property | Description |
|---|---|
| Source | Zenodo |
| DOI | [10.5281/zenodo.23376472](https://doi.org/10.5281/zenodo.23376472) |
| Total Images | 59 |
| Experimental Images | 57 |
| Negative Controls | 2 |
| Culture Media | MAC, MRS, PCA, VRBA |
| License | CC BY 4.0 |

The Dataset Contains Agar Plate Images From Different Culture Media, Including Negative Controls, Providing A Basis For Exploring Colony Detection And Enumeration.

Please Refer To The Original Dataset Record For Its Full Description, Usage Conditions, And Attribution Requirements.

## Methods

### 1. Classical Computer Vision — OpenCV

The Classical Approach Uses Image Processing And Contour Analysis To Identify Potential Bacterial Colonies.

The Pipeline Includes:

- Agar Plate Detection And Region Of Interest Extraction.
- Illumination Normalization.
- Adaptive Thresholding.
- Morphological Operations.
- Optional Grid Suppression.
- Contour Detection And Filtering.
- Colony Count Estimation.

This Method Provides A Lightweight And Interpretable Approach Without Requiring A Deep Learning Model.

**Notebook:** `Colony-Enumeration-Zenodo-Colab.ipynb`

### 2. Deep Learning — FastSAM

The Deep Learning Approach Uses FastSAM For Instance Segmentation And Detection Of Potential Colony Regions.

The Pipeline Includes:

- Agar Plate Region Detection Using OpenCV.
- FastSAM-Based Segmentation.
- Mask Processing And Filtering.
- Region Of Interest And Shape-Based Filtering.
- Colony Count Estimation.

FastSAM Is Used Without Additional Training On This Dataset, Making The Approach A Zero-Shot Segmentation Experiment.

**Notebook:** `Colony-Enumeration-FastSAM-DL-Colab.ipynb`

## Project Structure

```text
Colony-Enumeration/
│
├── Codes/
│   ├── Colony-Enumeration-Zenodo-Colab.ipynb
│   └── Colony-Enumeration-FastSAM-DL-Colab.ipynb
│
├── Sample-Results/
│   ├── Classical-Overview/
│   ├── Colony-Counts-VS-Dilution-Classical-OpenCV/
│   └── FastSAM-Overview/
│
├── README.md
├── Method-Comparison.md
└── requirements.txt
```

## Installation And Requirements

The Project Is Designed To Run In Google Colab.

The Main Dependencies Include:

- Python
- OpenCV
- NumPy
- Pandas
- Matplotlib
- Requests
- TQDM
- PyTorch
- Torchvision
- Ultralytics

Install The Required Packages Using:

```bash
pip install -r requirements.txt
```

**Note:** FastSAM Requires Downloading Its Pretrained Model Weights. A Compatible Runtime And Sufficient Computational Resources Are Recommended For Deep Learning Inference.

## How To Run

1. Open Google Colab.
2. Upload Or Open The Desired Notebook From The `Codes/` Directory.
3. Run The Notebook Cells In Order.
4. Allow The Dataset To Be Retrieved From Zenodo.
5. Execute The Image Processing Or Segmentation Pipeline.
6. Review The Generated Colony Counts And Sample Results.

For The Classical Approach, Use The OpenCV Notebook.

For The Deep Learning Approach, Use The FastSAM Notebook.

## Results And Comparison

Both Approaches Were Applied To The Selected Dataset To Explore Their Behavior Across Different Agar Plate Images.

The Project Includes Sample Outputs And A Separate Method Comparison Report.

For A More Detailed Discussion Of The Experimental Results, Differences Between The Two Approaches, And Observed Limitations, See:

**[Method Comparison: Classical OpenCV Vs FastSAM](Method-Comparison.md)**

## Limitations

This Project Is An Experimental Computer Vision Study And Should Not Be Considered A Validated Laboratory Colony Counting System.

Important Limitations Include:

- The Dataset Release Used In This Project Does Not Provide Official Per-Colony Ground-Truth Annotations Or Reference Counts.
- The Generated Counts Represent Algorithmic Estimates, Not Confirmed Microbiological Measurements.
- Classical Image Processing Can Be Sensitive To Illumination, Agar Appearance, Printed Grids, And Colony Overlap.
- FastSAM May Merge Neighboring Colonies Or Produce Multiple Segments For A Single Colony.
- Segmentation Quality And Counting Reliability May Vary Across Culture Media And Image Conditions.
- The Results Have Not Been Validated Against Independent Laboratory Reference Measurements.

Therefore, The Results Should Be Interpreted As Experimental Outputs Rather Than Definitive Colony Counts.

## Future Improvements

Potential Directions For Further Development Include:

- Evaluation With Expert-Verified Colony Annotations.
- Improved Detection Of Overlapping And Closely Spaced Colonies.
- More Robust Handling Of Different Agar Media And Lighting Conditions.
- Quantitative Evaluation Using Precision, Recall, And F1-Score When Ground-Truth Annotations Become Available.
- Exploration Of Additional Segmentation And Object Detection Models.

## Conclusion

This Project Demonstrates Two Different Approaches To Automated Bacterial Colony Enumeration Using Computer Vision.

By Combining A Classical OpenCV Pipeline With FastSAM-Based Segmentation, The Project Provides An Experimental Comparison Between Traditional Image Processing And Modern Deep Learning Techniques.

It Also Highlights The Challenges Of Automated Colony Counting And The Importance Of Reliable Ground-Truth Data And Laboratory Validation Before Practical Scientific Use.

## Acknowledgements

- **Dataset:** [Zenodo — Bacterial Colony Enumeration Dataset](https://doi.org/10.5281/zenodo.23376472)
- **OpenCV:** [Official Website](https://opencv.org/)
- **FastSAM:** [Ultralytics Documentation](https://docs.ultralytics.com/models/fast-sam/)

Dataset Usage And Attribution Must Follow The Original CC BY 4.0 License Terms.