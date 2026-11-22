# Method Comparison: Classical OpenCV vs FastSAM

## 1. Project Overview

This Project Compares Two Approaches For Bacterial Colony Enumeration From Agar Plate Images:

- **Classical OpenCV:** A Traditional Image Processing Pipeline Based On Thresholding, Morphological Operations, And Contour Analysis.
- **FastSAM:** A Deep Learning-Based Segmentation Approach Used To Identify Candidate Colony Regions.

Both Methods Were Evaluated Using The Same Zenodo Dataset And A Similar Plate Region Of Interest (ROI) Strategy.

**Dataset:** [Zenodo - Bacterial Colony Enumeration Benchmark](https://doi.org/10.5281/zenodo.23376472)

The Main Objective Was To Explore How Classical Image Processing And Zero-Shot Deep Learning Segmentation Perform Under Different Plate Conditions, Including Negative Controls.

## 2. Methods

### A. Classical OpenCV

The Classical Pipeline Uses Traditional Computer Vision Techniques To Detect And Count Candidate Colonies.

**Main Processing Steps:**

1. Detect The Circular Agar Plate Using Thresholding And Contour Analysis.
2. Apply Local Illumination Normalization.
3. Perform Adaptive Thresholding.
4. Use Morphological Operations To Clean The Binary Image.
5. Optionally Suppress Printed Grid Lines.
6. Detect Contours And Filter Them Using Area, Circularity, Aspect Ratio, And Solidity.

**Advantages:**
- Lightweight And Fast.
- Can Run On CPU Without A Dedicated GPU.
- Requires Relatively Few Dependencies.
- Processing Steps Are Transparent And Interpretable.
- Parameters Can Be Adjusted For Different Image Conditions.

**Limitations:**
- Sensitive To Printed Grid Lines And Background Patterns.
- Performance Depends On Agar Media Appearance And Image Quality.
- Finding One Parameter Configuration That Works Well Across All Plates Is Difficult.
- Strict Filtering Can Remove Real Colonies, While Loose Filtering Can Introduce False Detections.

### B. FastSAM

FastSAM Uses A Deep Learning-Based Segmentation Model To Identify Candidate Regions In Agar Plate Images.

The Plate ROI Is First Detected Using OpenCV. FastSAM Is Then Applied To Generate Segmentation Masks, Which Are Filtered Based On Their Position And geometric properties.

**Main Processing Steps:**

1. Detect The Agar Plate Region Using OpenCV.
2. Apply FastSAM To Generate Candidate Segmentation Masks.
3. Filter Masks Based On Their Location Inside The Plate.
4. Apply Size And Shape-Based Filtering.
5. Count The Remaining Candidate Regions As Detected Colonies.

**Advantages:**
- Better Handling Of Image Texture Variations In This Experiment.
- More Reliable Negative Control Results Compared With The Classical Pipeline.
- Reduces Some Of The Difficulties Associated With Manually Designed Thresholding Rules.

**Limitations:**
- Requires Additional Dependencies, Including Ultralytics.
- More Computationally Demanding Than Classical OpenCV.
- GPU Acceleration Is Recommended.
- Dense Colony Regions May Be Over-Segmented.
- Segmentation Masks Do Not Always Correspond To Individual Biological Colonies.

## 3. Experimental Setup

| Parameter | Description |
|---|---|
| Dataset | Zenodo Bacterial Colony Enumeration Benchmark |
| Dataset DOI | 10.5281/zenodo.23376472 |
| Total Images | 59 |
| Experimental Images | 57 |
| Negative Controls | 2 |
| Culture Media | MAC, MRS, PCA, VRBA |
| Classical Pipeline | OpenCV + NumPy |
| Deep Learning Pipeline | OpenCV + FastSAM |
| FastSAM Model | FastSAM-x |
| Evaluation | Predicted Colony Counts, Negative Control Behavior, And Visual Inspection |

Both Methods Were Applied To The Same Dataset.

The Evaluation Focused On The Following Points:

- Whether Negative Control Plates Produced Counts Close To Zero.
- Whether Plates With Visible Colonies Produced Higher Counts.
- How Each Method Behaved Across Different Media Types.
- Visual Quality Of The Detected Regions And Segmentation Overlays.

**Important:** The Dataset Release Used In This Project Does Not Provide Official Per-Colony Ground-Truth Masks Or Reference Counts. Therefore, The Reported Values Represent Algorithm Predictions Rather Than Experimentally Validated Colony Counts.

## 4. Experimental Results

### A. Negative Control Comparison

Negative Controls Are Particularly Important Because They Help Evaluate Whether A Method Incorrectly Detects Background Patterns Or Other Non-Colony Structures.

| Image | Classical OpenCV | FastSAM |
|---|---:|---:|
| PCA_NC_01.jpeg | ~20 | 1 |
| PCA_NC_02.jpeg | ~30 | 0 |
| **Mean NC Count** | **~25** | **0.5** |

FastSAM Produced Substantially Lower Counts On Both Negative Control Images.

In Comparison, The Classical Pipeline Detected Additional Regions, Mainly Due To The Printed Grid And Background Structures.

### B. FastSAM Sample Results

The Following Results Show Predicted Colony Counts For Selected Images:

| Image | Predicted Count |
|---|---:|
| PCA_NC_01.jpeg | 1 |
| PCA_NC_02.jpeg | 0 |
| MAC_0_01.jpeg | 288 |
| MAC_0_02.jpeg | 233 |
| MAC_0_03.jpeg | 179 |
| MAC_0_04.jpeg | 57 |
| MAC_1_01.jpeg | 77 |
| MAC_2_01.jpeg | 5 |
| MAC_3_02.jpeg | 2 |

The Mean Predicted Count For Non-Negative-Control Images In The FastSAM Results Was Approximately 65.4.

This Value Is An Algorithmic Output And Should Not Be Interpreted As A Verified Biological Measurement.

### C. Classical OpenCV Results

The Classical Pipeline Showed Considerable Sensitivity To Parameter Selection.

- With Loose Filtering, Printed Grid Structures And Background Patterns Were Frequently Detected As Colonies.
- With Strict Filtering, Negative Control Results Improved, But Some Real Colonies Were Also Excluded.
- The Estimated Mean Count For Non-Negative-Control Images Varied Across Tuning Stages, With Approximate Values Between 40 And 100.

These Results Highlight The Difficulty Of Applying A Fixed Set Of Traditional Image Processing Rules Across Different Agar Plate Conditions.

## 5. Comparative Analysis

| Evaluation Aspect | Classical OpenCV | FastSAM |
|---|---|---|
| Processing Approach | Traditional Image Processing | Deep Learning Segmentation |
| Computational Cost | Low | Higher |
| GPU Requirement | No | Recommended |
| Grid Line Sensitivity | High | Lower In This Experiment |
| Negative Control Behavior | Less Reliable | Better |
| Media Texture Variation | Sensitive | Better Handling In This Experiment |
| Dense Colony Regions | Depends On Thresholding And Filtering | May Over-Segment |
| Interpretability | High | Lower |
| Dependencies | Minimal | Additional Dependencies |
| Main Strength | Simplicity And Transparency | Segmentation Performance |

### What Worked Well

- FastSAM Distinguished Empty Negative Control Plates From Plates Containing Visible Colonies More Effectively In This Experiment.
- Classical OpenCV Provided A Lightweight And Explainable Baseline.
- Both Approaches Demonstrated The Potential Of Automated Image Analysis For Bacterial Colony Enumeration.
- A Rough Dilution-Related Trend Was Visible In Some Results, Although Considerable Variation Remained Between Images.

### What Did Not Work Perfectly

- Printed Grid Lines Were A Major Source Of False Detections For Classical OpenCV.
- Strict Classical Filtering Could Exclude Genuine Colony Regions.
- FastSAM Sometimes Produced Multiple Masks For Dense Or Complex Colony Regions.
- A Segmentation Mask Did Not Always Represent Exactly One Individual Colony.
- The Lack Of Ground-Truth Annotations Prevented Quantitative Validation Against Reference Colony Counts.

## 6. Practical Recommendation

The Two Methods Serve Different Purposes Within This Project.

**Classical OpenCV Is Suitable For:**
- Lightweight Computer Vision Applications.
- CPU-Based Processing.
- Explainable Image Processing Pipelines.
- Educational Demonstrations And Baseline Comparisons.

**FastSAM Is More Suitable For:**
- Exploring Deep Learning-Based Segmentation.
- Handling Some Of The Image Variations That Challenge Traditional Thresholding.
- Producing Better Negative Control Results In This Dataset.

For This Repository, The Recommended Approach Is To Keep Both Implementations:

- **FastSAM:** Primary Deep Learning-Based Approach.
- **Classical OpenCV:** Traditional Computer Vision Baseline.

This Provides A More Complete Comparison Between Conventional Image Processing And Modern Segmentation Techniques.

## 7. Limitations And Future Improvements

Several Limitations Should Be Considered When Interpreting The Results:

1. **No Ground-Truth Annotations:** The Dataset Release Used Here Does Not Include Official Per-Colony Masks Or Reference Counts.
2. **Predicted Counts:** The Reported Values Are Algorithm Outputs, Not Laboratory-Validated Measurements.
3. **Parameter Sensitivity:** Filtering Parameters And Segmentation Settings Can Affect The Final Counts.
4. **Different Media Types:** MAC, MRS, PCA, And VRBA Have Different Visual Characteristics, Which Can Influence Detection Performance.
5. **Colony Separation:** Overlapping Colonies, Merged Regions, And Fragmented Masks Can Affect Enumeration Accuracy.

Future Improvements Could Include Annotated Colony Datasets, Reference Counts Verified By Laboratory Experts, And More Extensive Evaluation Using Precision, Recall, And F1-Score.

## 8. Conclusion

This Project Explored Two Different Approaches To Automated Bacterial Colony Enumeration Using A Shared Agar Plate Image Dataset.

Classical OpenCV Demonstrated The Advantages Of Traditional Image Processing, Including Low Computational Cost, Transparency, And Simplicity. However, Its Performance Was Strongly Affected By Printed Grid Lines And Image Variations.

FastSAM Produced Better Negative Control Results In The Experiments Conducted And Handled Some Image Variations More Effectively. Nevertheless, Over-Segmentation And The Lack Of Ground-Truth Annotations Remain Important Limitations.

Overall, Combining Both Implementations Provides A Useful Practical Comparison Between Classical Computer Vision And Deep Learning-Based Image Segmentation.

**Project Status:** Experimental Comparison. The Reported Colony Counts Have Not Been Validated Against Laboratory Reference Measurements.