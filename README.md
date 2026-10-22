Predicting Anemia in Chronic Kidney Disease (CKD)
A beginner machine learning project. It tests whether routine kidney-function markers can predict anemia in patients with chronic kidney disease.
Not for clinical use. This is a learning project. The model is not validated and must not be used to make medical decisions.

The question
Anemia is common in CKD. It is normally found with a simple hemoglobin blood test.
I removed hemoglobin on purpose. This project asks a harder question:
Can the other routine markers (kidney function, urine tests, blood chemistry and medical history) predict anemia without the blood count?
The answer so far is: only modestly.

Dataset
Where I got it: a copy shared on Kaggle by [Nitesh Yadav ]:Chronic KIdney Disease dataset. Accessed on [29th August,2026]. Licence on Kaggle: Unknown          
Size: 400 patients, about 25 columns (lab values, urine tests, symptoms and history).
Target: ane (anemia: yes or no).
The data file is not included in this repo. See How to run to download it.

What I did
Cleaned the data.


Fixed stray tab characters in dm, cad and classification (for example "\tno").
Removed rows with a missing age or a missing anemia label.
Removed one impossible sodium value (below 20).
Checked for duplicate patients and found none.
Dropped hemo, pcv and rc. They define anemia directly, so keeping them would be a data leak.
Result: 400 patients became 389.
Explored the data with ten questions about age, comorbidities and lab values.


Kept CKD patients only. None of the 148 non-CKD patients has anemia. Keeping them would let a model score well by learning "healthy kidneys, so no anemia". After this step: 241 patients, 57 of them anemic (about 24%).


Built a pipeline. It fills blank values, scales the numbers, then predicts. Everything happens inside each test fold, so no test data leaks into training.


Compared three models with the same test: Logistic Regression, Random Forest and XGBoost. The test is 5-fold cross-validation repeated 5 times (25 scores per model).


Tested an idea. Do five extra features help? They are hypertension, diabetes, coronary artery disease, appetite and pedal oedema.



Results
Scores are the average across 25 tests, with the spread (± 1 standard deviation). Features: 11 lab values plus the 5 extra features.
Model
Recall
Precision
F1
ROC AUC
Logistic Regression
0.55 ± 0.13
0.36 ± 0.07
0.43 ± 0.08
0.64 ± 0.10
Random Forest
0.27 ± 0.12
0.75 ± 0.29 *
0.39 ± 0.15
0.70 ± 0.08
XGBoost
0.40 ± 0.13
0.50 ± 0.15
0.44 ± 0.12
0.66 ± 0.07

* Random Forest sometimes flagged no anemic patients in a test fold. Its precision is unstable, so do not trust that number.
Model comparison
How to read the scores
Recall: the share of anemic patients the model finds. This is the main score here, because a missed patient is the costly mistake.
Precision: the share of flagged patients who truly have anemia.
F1: one number that balances recall and precision.
ROC AUC: how well the model ranks patients. 0.5 is a coin toss and 1.0 is perfect.
What the results say
Logistic Regression is the best choice for the goal. It finds about 55 in every 100 anemic patients. The price is false alarms: about two in three flagged patients are not anemic.
It clearly beats Random Forest on recall. It does not clearly beat XGBoost. Their spreads overlap.
The 5 extra features changed almost nothing. Every score moved by less than its spread.
No model is good enough. ROC AUC is 0.64 to 0.70, well below the 0.80 usually seen as good.

Exploratory analysis highlights
CKD appears in every age group. It peaks at ages 60 to 69.
Among CKD patients, about 59% have hypertension and about 14% have coronary artery disease.
Hypertension and diabetes become more common with age.
None of the non-CKD patients with a recorded value has diabetes.

Limitations
Small data. Only 57 anemic patients. Scores vary a lot from split to split.
Missing values. Sodium and potassium are missing for about a third of CKD patients. Blanks are filled with the median.
No outside testing. Everything was tested inside one dataset. The model has not met patients from another hospital or country.
Missing information. No sex (so no eGFR), no CKD stage, no iron results and no medication list.
A simple test exists. Doctors check anemia with hemoglobin. This model only explores what the other markers can tell us.

Project structure
├── ANEMIA_PREDICTION.ipynb    # the full analysis
├── model_comparison.png       # results chart
├── requirements.txt           # libraries needed
└── README.md


How to run
Clone the repo and install the libraries:

 git clone <your-repo-url>
cd <repo-folder>
pip install -r requirements.txt


Download the dataset from the Kaggle page. A free Kaggle account is needed. Save the CSV as kidney_disease.csv in the project folder. 
Open the notebook and choose Restart & Run All:

 jupyter notebook ANEMIA_PREDICTION.ipynb



Ideas for next steps
Test on data from another hospital.
Add sex, CKD stage and medications.
Try a model that is allowed to say "not sure" for borderline patients.
Look at which features matter most in the Logistic Regression.

Acknowledgements
Thanks to Nitesh Yadav for sharing the dataset on Kaggle.
The data originally comes from the UCI Machine Learning Repository (Chronic Kidney Disease dataset). Please cite the original authors if you reuse it.

About
Built by Aguda Oluwagbohunmi as a learning project in data science for healthcare.
Questions or feedback are welcome. Please open an issue.

