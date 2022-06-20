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

### Outliers
Outliers can harm our models or reduce accuracy. The boxplots indicated that some could exist, so I opted to try an “Isolation Forest” to classify some outliers. Isolation Forest acts like a decision tree, but selects a feature and randomly splits along that value. This quickly isolates outliers due to their more extreme values, and they can be labeled as such and counted.  
This dataset had 52 outliers out of 569 observations which is a little over 9%, too much information to throw away, and since outliers could indicate abnormal or cancerous cells anyway, they should be kept.

### Collinearity
Since many of the features describe the same geometric shapes, it is likely that there is collinearity among them. Creating a correlation matrix, we can see there are 5 features with a correlation of about 0.9. This is an indication thatfeature selection is likely needed to produce a useful model.
![image](https://user-images.githubusercontent.com/60637235/174682040-1062bf5d-d4c9-4326-9b0f-b89f76834edf.png)

## Feature Engineering and Preprocessing
### Feature Scaling
To avoid outliers impacting feature scaling or model results, Robust Data Scaling was needed. This type of scaling is similar to minmax scaling, but rather than subtracting and scaling to the minimum and maximum (which could be outliers), the data is scaled to the first and third quartile.

### Dimensionality Reduction
Using a PCA analysis, the optimal number of principal components was between 6 and 7. Given that To reference how this was chose, please see page 6 [here](https://github.com/pogags/Breast-Cancer-Diagnostic/blob/main/Breast%20Cancer%20Diagnostic.pdf)

## Analysis
According to this study at the National Library of Medicine, sensitivity for Malignant cells can range between 65.4 – 92.4% for FNA and between 88.7 - 100% for core biopsies. This is what we will be evaluating our results against.  
We will be evaluating seven classification techniques against each other, and picking the best two to tune.

### Models
Before models were tuned they had the following accuracy on the training set  
•	SVC: 97.5%  
•	Logistic Regression: 98.0%  
•	K-Neighbors Classifier: 96.7%  
•	MLP Classifier: 98.7%  
•	Gaussian Naïve-Bayes: 94.4%  
•	Random Forest: 100%  
•	Decision Tree: 100%  

Decision Trees are prone to overfitting and Random Forests are similar in nature, so I opted to take Random Forest, MLP, and Logistic Regression Classifiers as my analysis tools and to cross validate them further to see which fit best.
Their cross validation scores are below.  
•	MLP Classifier: 96.7%  
•	Logistic Regression: 97.2%  
•	Random Forest: 93.4%  

Narrowed down to Logistic Regression and MLP, both models were given a grid search crossvalidation and had their hyper parameters tuned. Additionally, recall in this problem is a much more significant issue that specificity, since a false negative represents an incorrect diagnosis on a tumor of benign when it is actually malignant, which could result in the death of the patient. Maximizing recall limits false negatives. This was considered in the selection of a final model.

I performed two GSCVs (Grid Search Cross Validation) on each model, with accuracy optimized for one and recall optimized in the other. This produced two optimized two for each method. 
Of the 4 models produced via grid search, the **MLP optimized for Recall** and **Logistic Regression optimized for Accuracy** proved best.

### Classification Threshold Tuning

When making a classification these methods use the continous input variables to create a probability output variable. A probability over 0.5 is classified as 1, and under is classified as 0. Changing this threshold of 0.5 can greatly impact misclassifications. In this case, lowering the threshold will increase the number of false positives but decrease false negatives and improve recall.  

![image](https://user-images.githubusercontent.com/60637235/174684434-0fbae403-bc0a-4fbb-9e0e-2d84eb4c354a.png)
![image](https://user-images.githubusercontent.com/60637235/174684443-0bd2df73-4f85-464b-aeeb-2c4705a17115.png)

Though the MLP seemed to benefit from the threshold change from 0.5 to 0.35 (gained 3 true positives), the Logistic Regression classifier did not benefit as much at 0.35 (gained 1 true positives). Both had the best recall accuracy tradeoff at 0.35, so for the final models this was used.

## Testing and Conclusion

Testing the final models and their threshold values on the test set the following results were obtained:
![image](https://user-images.githubusercontent.com/60637235/174684509-f15cb67a-3053-4662-8c7b-65c4ed70dea0.png)

Though the recall for both is better than only an FNA analysis, the amount of false negatives that remain in the test data (4 each) suggest that both final models should be paired with FNA and human analysis if they do not detect a malignant tumor. More observations or more components in the PCA could help improve this model as well. Attempting another model with a random forest classifier without PCA could also provide useful results.

Here are the final restults for each model run on the test set with a resulting confusion matrix
### Logistic Regression
![image](https://user-images.githubusercontent.com/60637235/174684559-84681565-cbf1-4939-be68-cd328ad091ea.png)
### MLP
![image](https://user-images.githubusercontent.com/60637235/174684595-ecefad5d-0213-4735-9ff3-ace9a9696d26.png)


