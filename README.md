# MAI391_Financial-Wellness-Prediction-Model
**Name**: Chau Le Quan  
**StudentID**: SE192913

## Objectives
**Background and Motivation**:   
Financial Health is a term used to describe the state of one's personal monetary affairs. An individual's Financial Wellness often determined through spending habits, how they use and save money. But people in one group often have similar spending habits and saving.   

**Project Objective**:   
Predict Personal Financial Wellness based on Demographic Information.

## Result Achieved
- Trainning Multinomial Logistic Regression model with Accuracy = 0.84
- Traing Random Forest Classifier model with Accuracy = 0.843

## Data
**About Dataset:**  Dataset contains detailed financial and demographic data for 20,000 individuals in India, focusing on income, expenses, and potential savings across various categories. 

**Note**: The table below does not contain all the columns in the .csv file. Only relevant features are listed.
<table>
  <tr> <td><b>Parameter</td> <td><b>Description</td> </tr>
  <tr> <td>Income</td> <td>Monthly income in currency unit</td> </tr>
  <tr> <td>Age</td> <td>Age of the individual</td> </tr>
  <tr> <td>Dependents</td> <td>Number of dependents supported by the individual</td> </tr>
  <tr> <td>Occupation</td> <td>Type of employment or job role.</td> </tr>
  <tr> <td>City_Tier</td> <td>
    <ul>
    <li>Tier 1: Largest, most developed, and most densely populated cities.</li>
    <li>Tier 2: Mid-sized cities</li>
    <li>Tier 3: Small cities or towns with lower population densities and slower economic growth </li>
    </ul>
    </td>
  </tr>
  <tr>
    <td>Rent<br>Loan_Repayment<br>Insurance<br>Groceries<br>Transport<br>Eating_Out<br>Entertainment<br>Utilities<br>Healthcare<br>Education<br>Miscellaneous</td>
    <td>Monthly Expenses Records </td>
  </tr>
  <tr> <td>Desired_Savings</td> <td>Targets for monthly saving</td> </tr>
  <tr> <td>Disposable_Income</td> <td>Income remaining after all expenses are accounted for</td> </tr>
</table>

## Approach
  - Create `Neccessary_Expense` feature (including <ins>Rent</ins>, <ins>Insurance</ins>, <ins>Groceries</ins>, <ins>Transport</ins>, <ins>Utilities</ins>, <ins>Healthcare</ins>, <ins>Education</ins>)
- Create `Discretionary_Expenses` feature (including <ins>Entertainment</ins>, <ins>Eating_Out</ins>)
- Calculating "Finacial Point" through Financial Scoring System
    - Budget Adherence: Monthly expenses stays within income amount (1 point)
    - Essential vs. Discreationary Spending: Ideally, no more than 30% of income should go toward discretionary expenses (`Entertainent`, `Eating_Out` and 35% of `Miscellaneous`). (1 point)  
    - Saving Rate: (We assume that 65% of `Disposable_Income` value would be the Actual Saving amount)
        - if the Actual Saving amount satisfied the `Desired_Saving` (0.5 point)
        - if the Actual Saving amount is about 15%-20% of income (1 point)
    - Debt Management: DTI Ratio (Debt-to-Income Ratio) below 36% of income (1 point)

- Add a lable to determine personal financial health based on "Financial Point"
    - (4 - 4,5]: Good Financial Health (2)
    - (3 - 3.5]: Average Financial Health (1)
    - <3: Vulnerable Financial Health (0)

- EDA: Determine correlations between features in the dataset.
  
- Building model
  - Target Variable: `Fin_Health` (stands for Financial Health)
  - Predictors: [`Income`, `Age`, `Dependents`, `Loan_Repayment`, `Occupation`, `City_Tier`]
  - Models: <ins>Multinomial Logistic Regression</ins>/ <ins>Random Forest</ins>

## Descriptive Statistic

**Descriptive Statistic Table**

<img src="https://github.com/user-attachments/assets/949383a5-1f82-48d9-b112-2d17a2507084" alt="image" width="610"/><img src="https://github.com/user-attachments/assets/c603b3c4-bc18-4894-8209-dea6ea324ee5" alt="image" width="340"/>

## Visualization and Correlation
**Correlation Heatmap**
<table>
  <td> 
    <img src="https://github.com/user-attachments/assets/02ded40c-3fe0-4b6f-a54e-3543b60a6f4f" alt="image" width="500"/>
  </td>

  <td>
    Looking into this correlation heatmap, there are some interesting insights that we can explore deeper:<br>
    - Age have no correlation with any other variables.<br>
    - City_Tier have slightly negative correlation with Rent, meaning that the larger the city, the higher the rent fee would be. <br>
    - Loan_Repayment have a significantly negative correlation with Financial Health, it is reasonable since we take into acount DTI Ratio when calculating Financial Point. <br>
    &rarr; Does Loan_Repayment amount have any correlation with Age/Age_Group, Occupation or Dependents? <br>
    - Rent have negative impact on Financial Health<br>
    - Dependents column have negative correlation with Financial Health status<br>
  </td>
  
</table>

### 1. Age, Income and Occupation: Have no identifiable correlation
In this correlation heatmap, we can see that `Age` and `Income` have no correlation. But it is more reasonable to think that `Age` and `Income` should have a positive linear relationship, isn't it? 

**Scatter Plot of Age and Income**   

<table>
  <td><img src="https://github.com/user-attachments/assets/164cd41a-2102-4cb1-bc96-0c20346d459e" alt="image" width="550"/></td>
  <td>
    &rarr; While there is a really big gap between mininum and maximum value of the amount of income, the income amounts of all age group are almost identical and indistinguishable.
  </td>
</table>

**Boxplot of Income Distribution between Occupation**

<table>
  <td><img src="https://github.com/user-attachments/assets/0e35e2d6-299e-4ce4-bb8d-eed7ba4f00b7" alt="image" width="300"/></td>
  <td>
    There are 4 types of Occupations in the Dataset, which is: Student, Professional, Self_Employed and Retired.<br>
    &rarr; Income distributions are almost identical between 4 types of Occupations.
  </td>
</table>

## Model and Evaluation
### Preparation for Model Development

- Encode categorical variables (`City_Tier`/ `Occupation`) using One-Hot Encoding
- Feature Selection:
  - Target Variable: `Fin_Health` (stands for Financial Health)
  - Predictors: [`Income`, `Age`, `Dependents`, `Loan_Repayment`, `Occupation`(encoded Occupation), `City_Tier`(encoded City_Tier)]
- Spliting encoded dataset into Train set and Test set (70/30 ratio)
- Scaling data using StandardScaler
  
### 1. Multivariate Logistic Regression

**Why:** Since the objective of the project is to predict Financial Wellness based on Demographic information. Logistic regression is simple and effective for classification. 

**Model**: LogisticRegression from Scikit-learn Library  

| | Using Default Parameter | Using Parameter after tuning using GridSearchCV |
|--|--|--|
| solver | lbfgs | saga |
| C | | 10| 
|max_iter| | 100|
| confusion_matrix| <img src="https://github.com/user-attachments/assets/e8d35f11-1f0e-4ff2-a043-c8ef4e2cad87" alt="image"/>|<img src="https://github.com/user-attachments/assets/6b47ba04-0ac6-4d53-95ac-b71c245de70a" alt="image"/> |
| classification_report |<img src="https://github.com/user-attachments/assets/11afa289-bdad-4723-9ae0-f08cb5cb9634" alt="image"/>|<img src="https://github.com/user-attachments/assets/c1ca19c0-de0c-425a-86bf-743183696e5c" alt="image"/> |

### 2. Random Forest

**Why:** Random forests build an ensemble of decision trees, which usually increases predictive accuracy by reducing overfitting. They can capture complex patterns and interactions between demographic factors and financial wellness.

**Model:** RandomForestClassifier from Scikit-learn Library

| Result | |
|--|--|
| accuracy_score | 0.8438333333333333 |
| confusion_matrix | <img src="https://github.com/user-attachments/assets/744a9d8a-eb03-49e3-bf12-fb2ad31dd9ef" alt="image"/>|
|classification_report| <img src="https://github.com/user-attachments/assets/d1238bca-5712-496c-a25c-84c955a84b24" alt="image" width=300/>|

One of the benefits of Random Forest is that it provides feature importance scores, which show which features are most predictive.

<table>
  <td>
    <img src="https://github.com/user-attachments/assets/8c42c567-89f4-4457-ba5e-623737fc3b83" alt="image"/>
  </td>
  <td>
    - Even though we can not specify the correlation between Age and other variable, it seems to be a good predictor for the financial health.<br>
    - As we see in the EDA section, the occupation distribution do not really tell the story of financial health.<br>
    - On the other hand, Loan Repayment amount can be quite predictive
  </td>
</table>

### 3. K-Nearest Neighbors 

**Why:** KNN is a simple, instance-based learner that predicts based on similarity to other observations. It can work well if there are clear clusters in demographics associated with financial wellness.

**Model:** KNeighborsClassifier from Scikit-learn Library

| Result | |
|--|--|
| accuracy_score | 0.8326666666666667 |
|classification_report| <img src="https://github.com/user-attachments/assets/016dc3b1-d337-439b-ba34-ad77e138ab17" alt="image" width=300/>|

## Significance

## Conclusion
