# IOAI Radar Semantic Segmentation

## Solution Overview
This repository contains my solution to the IOAI Radar problem.

I implemented a UNet architecture from scratch in PyTorch and experimented with different approaches, including SegNet and UNet (UNet performed best).

Key components:
- Custom dataset classes for train and test sets
- Data augmentation
- Class weighting to address scoring imbalance (-1 vs other classes)
- Learning rate scheduler to reduce overfitting
- Training for up to 40 epochs

Final score: **0.98**

---

## 1. Problem Description
Radar is a key technology in wireless communication, with widespread applications such as self-driving cars. It typically involves an antenna that transmits specific signals and receives their reflections from objects in the environment. By processing these signals, the system determines the angular direction, distance, and velocity of target objects.

In real-world applications, radar signal processing is challenging due to noise and reflections from non-target objects in the surroundings. For example, when attempting to detect pedestrians, the radar may also receive reflections from trees or other background objects, which can degrade accuracy. Your task is to use AI to analyze the signals received by the radar and identify the presence of a human at each position.

In this task, we provide an indoor radar experiment dataset, and your objective is to develop a model that performs radar semantic segmentation.

## 2. Dataset
To measure objects surrounding a radar, the following key parameters are used:

Range: The straight-line distance between the radar and an object.  
Azimuth: The horizontal angle (left to right) between the radar and the object.  
Elevation: The vertical angle (up or down) of the object relative to the radar.  
Velocity: The speed at which the object is moving toward or away from the radar.  

The radar data is processed into multiple heatmaps, each encoding the received signal strength at various positions and directions.

Static heatmaps emphasize reflections from stationary objects.  
Dynamic heatmaps highlight changes caused by moving objects.  

When no object is present at a specific location, the signal consists mostly of background noise and appears weak. In contrast, reflections from an object increase signal intensity, enabling detection of the object.

For example, the static range-azimuth heatmap represents signal strength across different distances (range) and horizontal angles (azimuth), mainly reflected by stationary objects.

Each sample in the dataset is stored in a .mat.pt file as a tensor of shape  7×50×181 , where:

7 is the number of maps (6 heatmaps + 1 semantic label map),  
50 represents range bins (distance),  
181 represents angular or velocity bins, covering angles from -90° to +90° in either the horizontal or vertical plane. You can assume that the velocity bins are also remapped from -90° to +90° for visualization consistency.  

each heatmap intensity value is normalized to [0, 1], representing received signal strength.  

The 6 heatmaps are structured as follows:

Index 0: Static range-azimuth heatmap  
Index 1: Dynamic range-azimuth heatmap  
Index 2: Static range-elevation heatmap  
Index 3: Dynamic range-elevation heatmap  
Index 4: Static range-velocity heatmap  
Index 5: Dynamic range-velocity heatmap  

All values in heatmaps are normalized, so no unit conversion is required.

The map at Index 6 is the semantic label map, stored in range-azimuth format.

-1: Background (no target)  
0: Suitcase  
1: Chair  
2: Human  
3: Wall  

Data scale: 1800 samples in the training set, 500 samples in the validation set, and 500 samples in the test set.

## 3. Task
Your task is to develop a model that takes the first six heatmaps (indices 0 to 5) as input, and predicts the semantic label map (index 6) as the output. The goal is to accurately identify what the target is(-1 to 3) at each location in the radar’s field of view.

Input: A tensor of shape  6×50×181  
Output: A tensor of shape  50×181  

## 4. Submission
Please submit a file named submission.ipynb. The output is a zip file named "submission.zip", which contains two tables submission_val.csv and submission_test.csv corresponding to the prediction results of the validation set and the test set respectively.

Note: The output table should have a header, the data in the table is not the actual solved data, it is only used as an example of the submission format.

## 5. Score
The score is based on the accuracy of label recognition. Correctly identifying target points is weighted more heavily than correctly identifying background points.

Scoring Criteria:
Each correctly identified background pixel earns 1 point.  
Each correctly identified non-background pixel earns 50 points.  

The final score is normalized to a 0-1 point by comparing it to the maximum possible score.

## 6. Baseline and Training Set
Baseline score: 0.67  
Top score (committee): 0.90
