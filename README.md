
# Introduction 
Machine learning (ML) has become an increasingly powerful tool in drug discovery. By leveraging the wealth of data in ChEMBL, ML models can accelerate and optimize various stages of the drug discovery process.

![image](https://github.com/user-attachments/assets/734f70c6-3b30-49df-af68-95a6564fd4fd)


ChEMBL provides a rich source of bioactivity and chemical data, which, when combined with machine learning algorithms, can significantly enhance the drug discovery process. ML allows researchers to make more accurate predictions, prioritize compounds, and identify promising drug candidates more efficiently.

The ChEMBL Database is a database that contains curated bioactivity data of more than 2 million compounds. It is compiled from more than 76,000 documents, 1.2 million assays and the data spans 13,000 targets and 1,800 cells and 33,000 indications. [Data as of March 25, 2020; ChEMBL version 26].

Here is the connection to the ChEMBL website: https://www.ebi.ac.uk/chembl/

In this project, I builded a machine learning model to predict bioactivity of a target compound using the ChEMBL bioactivity data.


## Part 1 - Data Collection and Pre-Processing
In Part 1, I performed data collection and pre-processing from the ChEMBL Database.
Acetylcholinesterase is the target protein, which is related to Alzheimer disease.

Among 29 targets for acetylcholinesterase, CHEMBL220(Human Acetylcholinesterase) is selected as it is a single protein and organism is homo sapiens. 

![image](https://github.com/user-attachments/assets/6137dc98-0fea-4b5e-b26d-b3578b871191)

The standard value (activity), such as IC50, EC50, Ki, or their normalized forms (e.g., pIC50), is critically important for drug-like proteins because it quantifies how effectively a compound interacts with a target protein. This value provides insight into the compound's bioactivity, a key determinant in drug discovery and development. 

After data pre-processing of the data, all compounds are labeled as either being active, inactive or intermediate.
The bioactivity data is in the IC50 unit. Compounds having values of less than 1000 nM will be considered to be **active** while those greater than 10,000 nM will be considered to be **inactive**. As for those values in between 1,000 and 10,000 nM will be referred to as **intermediate**.

## Part 2 - Explatory Data Analysis and Descriptor Calculation

In Part 2, I performed Descriptor Calculation and Exploratory Data Analysis.

### Calculation of Lipinski descriptors

Christopher Lipinski, a scientist at Pfizer, developed a set of guidelines known as the Rule of Five to evaluate the druglikeness of compounds. These guidelines are based on the ADME properties—Absorption, Distribution, Metabolism, and Excretion—collectively referred to as the pharmacokinetic profile. Lipinski formulated these rules by analyzing all FDA-approved orally active drugs.
The Lipinski's Rule stated the following:

- Molecular weight < 500 Dalton
- Octanol-water partition coefficient (LogP) < 5
- Hydrogen bond donors < 5
- Hydrogen bond acceptors < 10

### Convertion of IC50 to pIC50
To allow IC50 data to be more uniformly distributed, I converted IC50 to the negative logarithmic scale which is essentially -log10(IC50).

This custom function pIC50() will accept a DataFrame as input and will:

- Take the IC50 values from the standard_value column and converts it from nM to M by multiplying the value by 10
- Take the molar value and apply -log10
- Delete the standard_value column and create a new pIC50 column

### Explatory Data Analysis and Statistical Tests

The scatter plot of MW vs. LogP shows that the two bioactivity classes (active and inactive) occupy similar chemical spaces.

![image](https://github.com/user-attachments/assets/a0821acd-fcc7-4c2f-9931-a93c57375945)

However, examining the pIC50 values reveals a statistically significant difference between the active and inactive classes. This is expected, as the classification thresholds (IC50 < 1,000 nM = Active and IC50 > 10,000 nM = Inactive, corresponding to pIC50 > 6 = Active and pIC50 < 5 = Inactive) were used to define these categories.

![image](https://github.com/user-attachments/assets/0b231f5a-57bc-4998-9ea8-1533bccdd971)

According to Mann-Whitney U Test, all of the 4 Lipinski's descriptors(pIC50, MW, LogP, NumHDonors) exhibited statistically significant difference between the active and inactive bioactivity classes.

## Part 3 - Fingerprint Descriptors Calculation

In Part 3, I calculated molecular descriptors that are essentially quantitative description of the compounds in the dataset. Finally, I prepared a dataset for model building in Part 4.

In this section, I used **Google Colab** environment to run the codes.

I used the *bioactivity_data_pIC50.csv* file which I created in Part 2, containing the pIC50 values that we will be used for a regression model in modeling part 4.

I calculated fingerprint descriptors using PaDEL-descriptors: https://onlinelibrary.wiley.com/doi/full/10.1002/jcc.21707

This calculation method removes salts from the input molecules, standardizes nitro groups and finally calculates fingerprint descriptors as follows:

![image](https://github.com/user-attachments/assets/624f2cdc-511f-4105-a23f-d884ed5e83c9)

## Part 4 - Random Forest Model

In Part 4, I builded a regression model using the random forest algorithm.

After removing low variance features , data contained 6217 rows and 135 columns(which was 881 before).

Before tuning the first model, performence metrics were calculated as follows:

![image](https://github.com/user-attachments/assets/a8085cad-b895-409f-b1c6-c294d48d6580)

After hyperparameter tuning, performance of the model enhanced resulting in less error and increased R2 score:

![image](https://github.com/user-attachments/assets/9c36fcb0-9531-45cb-a310-81f44e49b027)

I recorded the model as "**acetylcholinesterase_model.pkl**" file.

## Part 5 - Streamlit Application

In terminal, run app.py file(I used VS Code).

```
streamlit run app.py
```

In your browser, you will see the page below:

![image](https://github.com/user-attachments/assets/dc54100c-3b5f-4e93-8e4b-680cc1f14208)

Download the example txt file in the left.

Upload your example file and predict the results.

![image](https://github.com/user-attachments/assets/52ab7a45-0015-406b-95f1-a04611c5d02a)

You can see the output data, calculated molecular descriptors, subset of descriptors and prediction output.
You can also download the prediction output as a csv file.
















