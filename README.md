# Breast-Cancer-Diagnostic
<sub><sup>For a full detailed report with more graphics, click [here](https://github.com/pogags/Breast-Cancer-Diagnostic/blob/main/Breast%20Cancer%20Diagnostic.pdf)</sup></sub>
## Introduction

The use of machine learning models in visual data fields is increasing, and healthcare is no exception. It is in the best interests of the patients, healthcare professionals, and insurance companies to make the most accurate diagnosis possible, and machine learning can be used as a powerful tool for that.


## Dataset

This dataset was given on Kaggle [here](https://www.kaggle.com/datasets/uciml/breast-cancer-wisconsin-data) and describes digitized images of an FNA (Fine Needle Aspirate) of breast mass. FNA is a minimally invasive and cost-effective sampling that obtains tissues from a part of the body. The alternative to this a core biopsy, which provides more information but is more invasive, expensive, and takes longer to process. To improve medical outcomes for citizens, better access to healthcare is paramount. Better predictions with an FNA tissue sample could lead to cheaper tests and faster results with the same sensitivity as a Biopsy.

### Features

Dataset has 32 features, 1 target class, and 569 observations. There are 10 continuous variables, and for each a mean, standard error, and ‘worst’ measurement is taken (worst being the mean of the largest 3 measurements). 

•	ID number (Meaningless, dropped)  
•	Diagnosis (M = Malignant, B = Benign)  
 &emsp; o Binary Target Class, later M and B will be encoded as 1 and 0 respectively  
•	Continuous variables (3 of each – mean, standard error, ‘worst’)  
&emsp;  o	radius (mean of distances from center to points on the perimeter)  
 &emsp; o	texture (standard deviation of gray-scale values)  
 &emsp; o	perimeter  
 &emsp; o	area  
 &emsp; o	smoothness (local variation in radius lengths)  
&emsp;  o	compactness (perimeter^2 / area - 1.0)  
 &emsp; o	concavity (severity of concave portions of the contour)  
&emsp;  o	concave points (number of concave portions of the contour)  
 &emsp; o	fractal dimension ("coastline approximation" - 1)  
•	Unnamed (dropped)  

This analysis uses only the 30 continous features and the Diagnosis target variable.

### Data Exploration

The diagnosis slightly favored Benign classifications
![image](https://user-images.githubusercontent.com/60637235/174681631-ae8d2be1-268b-4796-9193-94e6e3145391.png)

Examining some box plots of the data, we can clearly see there is a wide distribution of the data and possibly even some outliers
![image](https://user-images.githubusercontent.com/60637235/174681781-ac3cb4b0-0f6c-441f-9542-1ad76008e8ff.png)

This Violin Plot gives an overview of how the data is distributed.
![image](https://user-images.githubusercontent.com/60637235/174681800-df0c4a8e-2dbb-42d9-a0f0-523dc3bf455b.png)

#### Outliers
Outliers can harm our models or reduce accuracy. The boxplots indicated that some could exist, so I opted to try an “Isolation Forest” to classify some outliers. Isolation Forest acts like a decision tree, but selects a feature and randomly splits along that value. This quickly isolates outliers due to their more extreme values, and they can be labeled as such and counted.  
This dataset had 52 outliers out of 569 observations which is a little over 9%, too much information to throw away, and since outliers could indicate abnormal or cancerous cells anyway, they should be kept.

#### Collinearity

Since many of the features describe the same geometric shapes, it is likely that there is collinearity among them. Creating a correlation matrix, we can see there are 5 features with a correlation of about 0.9. This is an indication thatfeature selection is likely needed to produce a useful model.
![image](https://user-images.githubusercontent.com/60637235/174682040-1062bf5d-d4c9-4326-9b0f-b89f76834edf.png)

## Feature Engineering and Preprocessing

