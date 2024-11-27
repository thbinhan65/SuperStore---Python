
## Project Title: SuperStore - RFM Analysis for Customer Segmentation

## Introduction:

Welcome to the SuperStore analysis project! This repository focuses on analyzing customer value to help SuperStore identify distinct customer segments. The goal is to inform targeted marketing campaigns during the holiday season, enhancing customer appreciation and loyalty.

## Objective:
- Utilize the RFM model to analyze customer data, segmenting customers based on their purchasing behavior. 
- This analysis will aid in crafting tailored marketing strategies to engage existing customers and attract potential loyal customers. 
- The project will leverage large datasets to automate segmentation, moving beyond manual methods.

## Analysis Focus:

1. **Data Preparation:**
   - Prepare the dataset suitable for the RFM model.
  
2. **RFM Score Calculation:**
   - Define and calculate Recency (R), Frequency (F), and Monetary (M) scores for each customer, using December 31, 2011, as the reference date.

3. **Scoring Methodology:**
   - Implement a scoring system from 1 to 5 using quintile methods for statistical analysis.

4. **Customer Grouping:**
   - Use the classification table to group customers into segments based on their RFM scores.

5. **Visualization:**
   - Visualize the distribution of segments across various dimensions.

6. **Current Status Analysis:**
   - Analyze the current state of the company and provide insights for the marketing team.

7. **Strategic Recommendations:**
   - Suggest key metrics for the marketing and sales teams to focus on, tailored to SuperStore's retail model.

## Tools and Techniques Used:

- Python
- Data Analysis Libraries (e.g., Pandas, NumPy)
- Data Visualization Libraries (e.g., Matplotlib, Seaborn)

## Execution Process:
- **Data Collection:** Gather the relevant customer dataset from internal sources.
- **Data Cleaning:** Clean the dataset to ensure consistency and accuracy for analysis.
- **Metric Calculation:** Calculate R, F, and M scores using appropriate methods.
- **Segmentation:** Classify customers into segments based on their scores.
- **Visualization:** Create visual representations of the segments for better understanding.
- **Analysis and Recommendations:** Analyze the results and provide actionable insights for the marketing team.

## Results Achieved:
- Successfully segmented customers using the RFM model.
- Automated the analysis process, reducing reliance on manual calculations.
- Provided valuable insights to inform targeted marketing strategies.
- Enhanced understanding of customer behavior to improve engagement and retention efforts.

## Project processing workflow

### Step 1: Import  and read file
```Python
# Import library
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# Read file
ecom_retail = pd.read_excel('/content/drive/MyDrive/Colab Notebooks/ecommerce retail.xlsx')
print(ecom_retail.head())
```

- Result:

![{4A96017A-581A-458C-A747-919C4A93DEFC}](https://github.com/user-attachments/assets/1b00a8c9-f16b-480c-99fb-fbc997cebb4b)

### Step 2: Clean, check data
#### Step 2.1: check null, dupliated, dtype, description
```Python
ecom_retail_null = ecom_retail.isnull().sum()
ecom_retail_duplicated = ecom_retail.duplicated().sum()
ecom_retail.dtypes

print(ecom_retail.describe())

print(ecom_retail_null)

print(ecom_retail_duplicated)

# After checking the data, there are 1,454 null values in the 'Description' column, and 135,080 null values in the 'CustomerID' column.

# There are 5,268 duplicated records.

# In the 'Quantity' and 'UnitPrice' columns, there are negative values.

# The main data types are datetime64ns, float64(2), int64(1), object(4).

# This dataset ranges from '2010-12-01' to '2011-12-09'.
```

- ResultL

![{6B665BCD-BE65-485D-B517-C7002E35A8E4}](https://github.com/user-attachments/assets/dac8009c-0d34-47f4-9e51-ec32b2ee7dfb)

#### Step 2.2: Data Processing
```Python
# Remove null values
ecom_retail_not_null = ecom_retail.dropna()

#Remove negative values in 'Quantity' and 'UnitPrice'
ecom_retail_last_dataset = ecom_retail_not_null[(ecom_retail_not_null['Quantity'] > 0) & (ecom_retail_not_null['UnitPrice'] > 0)]

print(ecom_retail_last_dataset.head())
```

- Result:

![{89ABD2CE-1288-4793-AB8C-98F849B54F89}](https://github.com/user-attachments/assets/b17451c3-98cd-48dd-a22f-da5974dab516)


### Step 3: Calculate RFM Score
#### Recency - R_Score:
```Python
Recency = ecom_retail_last_dataset.groupby(by="CustomerID", as_index=False).InvoiceDate.max()
Recency.columns = ["CustomerID", "LastDate"]
Recency["Recency"] = (Recency["LastDate"].max() - Recency["LastDate"]).dt.days
Recency['R_score'] = pd.qcut(Recency['Recency'], 5, labels=range(1, 6))
print(Recency)
```

- Result:

![{B23F6AB9-B4BA-40AE-84D6-00A260836771}](https://github.com/user-attachments/assets/85a0d7c6-b28d-478a-976a-3a666758b861)

#### Frequency - F_Score:
```Python
Frequency = ecom_retail_last_dataset.groupby(by="CustomerID", as_index=False).InvoiceDate.count().sort_values(by="InvoiceDate", ascending=True)
Frequency.columns = ["CustomerID", "Frequency"]
Frequency['F_score'] = pd.qcut(Frequency['Frequency'], 5, labels=range(1, 6))
print(Frequency)
```

- Result:

![{1764DBE4-BAFE-4666-9710-ABC4F713CC87}](https://github.com/user-attachments/assets/6c76737f-f458-44e7-bd8e-21ebb7df0e1e)

#### Monetary - M_score:
```Python
Monetary = ecom_retail_last_dataset.groupby(by="CustomerID", as_index=False).UnitPrice.sum().sort_values(by="UnitPrice", ascending=True)
Monetary.columns = ["CustomerID", "Monetary"]
Monetary['M_score'] = pd.qcut(Monetary['Monetary'], 5, labels=range(1, 6))
print(Monetary)
```

- Reuslt:

![{AC9BEEF2-E071-4906-B4B4-AD89027FD7B6}](https://github.com/user-attachments/assets/80658d26-3ff2-4d8a-be87-c9e36e3faa9c)


### Step 4: Classification & Visualization 
```Python
R_result = Recency[['CustomerID', 'R_score']]
F_result = Frequency[['CustomerID', 'F_score']]
M_result = Monetary[['CustomerID', 'M_score']]
RFM = pd.merge(R_result, F_result, on='CustomerID')
RFM = pd.merge(RFM, M_result, on='CustomerID')
RFM_dataset = ecom_retail_last_dataset.merge(RFM, on='CustomerID')
RFM_dataset.drop_duplicates(subset='CustomerID', keep='first', inplace=True)
RFM_dataset['R_score'] = RFM_dataset['R_score'].astype(str)
RFM_dataset['F_score'] = RFM_dataset['F_score'].astype(str)
RFM_dataset['M_score'] = RFM_dataset['M_score'].astype(str)
RFM_dataset['RFM_score'] = (RFM_dataset['R_score'] + RFM_dataset['F_score'] + RFM_dataset['M_score'])
RFM_dataset['RFM_score'] = RFM_dataset['RFM_score'].apply(float)
def segmentrow(row):
    f = row['RFM_score']
    if f in (555, 554, 544, 545, 454, 455, 445):
        return 'Best Customers'
    elif f in (543, 444, 435, 355, 354, 345, 344, 335):
        return'Loyal'
    elif f in (553, 551, 552, 541, 542, 533, 532, 531, 452, 451, 442, 441, 431, 453, 433, 432, 423, 353, 352, 351, 342, 341, 333, 323):
      return'Potential Loyalist'
    elif f in (512, 511, 422, 421, 412, 411, 311):
      return'New Customers'
    elif f in (525, 524, 523, 522, 521, 515, 514, 513, 425,424, 413,414,415, 315, 314, 313):
      return'Promising'
    elif f in (535, 534, 443, 434, 343, 334, 325, 324):
      return'Need Attention'
    elif f in (331, 321, 312, 221, 213, 231, 241, 251):
      return'About To Sleep'
    elif f in (255, 254, 245, 244, 253, 252, 243, 242, 235, 234, 225, 224, 153, 152, 145, 143, 142, 135, 134, 133, 125, 124):
      return'At Risk'
    elif f in (155, 154, 144, 214,215,115, 114, 113):
      return'Cannot  lose them'
    elif f in (332, 322, 233, 232, 223, 222, 132, 123, 122, 212, 211):
      return'Hibernating customers'
    elif f in (111, 112, 121, 131,141,151):
      return'Lost'
    else:
      return 'Unclassified'
RFM_dataset['Segment'] = RFM_dataset.apply(segmentrow, axis=1)
RFM_dataset.sort_values(by='CustomerID', ascending=True).head()
#RFM_segment_count = RFM_dataset.groupby('Segment').size()
#RFM_dataset.groupby('Segment').agg({'Recency': 'mean', 'Frequency': 'mean', 'Monetary': 'mean'})
RFM_dataset_summary = RFM_dataset.groupby('Segment').agg({'Quantity': 'sum', 'UnitPrice': 'sum'}).sort_values(by='Segment', ascending=True)
print(RFM_dataset_summary)
sns.countplot(x='Segment', data=RFM_dataset)
plt.xticks(rotation=45, ha='right')
plt.show()

sns.lineplot(x='Segment',y='Quantity', data=RFM_dataset_summary)
plt.xticks(rotation=45, ha='right')
plt.show()

sns.lineplot(x='Segment',y='UnitPrice',data=RFM_dataset_summary)
plt.xticks(rotation=45, ha='right')
plt.show()
```

- Result:

![{96742F3C-AD26-48A4-8526-6D45A7B3654B}](https://github.com/user-attachments/assets/cb5bb36c-4bc0-4bbb-b387-fa856cbee9fb)

![{9169EE97-7C9E-4F13-A32D-C8580DE9A2C6}](https://github.com/user-attachments/assets/36eea8af-61ed-4d6a-abde-813602203af0)

![{BB8B540E-E1BE-43E9-B12C-9B2BC774E7D2}](https://github.com/user-attachments/assets/8467c9a2-84db-4928-b46d-8cc604e44713)

![{79D37A2D-A364-4137-806E-A69486A2D88C}](https://github.com/user-attachments/assets/247455bc-d219-4cb2-8c6f-0cfd93e9a451)


## Analysis of the Company's Current Situation and Suggestions for the Marketing Team
Overview:
- The company has various customer segments, ranging from loyal customers to "Potential Loyalists" and "Hibernating Customers."

- The number of 'New Customers' is high in both customer count and the number of products sold. However, the Unit Price remains at an average level.

- The number of customers in some segments, such as "Best Customers," is relatively low, while "Cannot Lose Them" and "Hibernating Customers" are quite large, indicating that these are important segments that need attention.

- The 'Promising' segment, although having a low 'Quantity,' shows a high Unit Price, suggesting that this group has the potential to become a highly valuable customer base.

## Suggestions for the Marketing Team:

- Focus on retaining and increasing the value of loyal customers ('Loyal').
- Convert 'New Customers' into potential loyalists ('Potential Loyalist'). This segment provides stable revenue and high profit for the company in the medium term.
- Develop marketing campaigns to reactivate and attract 'Hibernating Customers,' 'At Risk,' and 'Need Attention.' This can be done through promotions, discounts, or direct outreach.
- Conduct deeper research and analysis on the "Best Customers" and "Cannot Lose Them" segments to better understand their needs and behaviors, allowing for the development of appropriate marketing strategies.

## Which Metric Should Be the Focus Among R, F, M?
For Superstore, based on the report, the most important metric for the Marketing and Sales teams to focus on is:
- All three metrics, but we will prioritize Monetary (M) - Average Order Value.

- Comparison Between 'New Customers' and 'Promising': We can see a clear difference in Quantity and Unit Price.

   - For 'New Customers,' the number of products is high, but the average value is not as high as expected. This indicates that the products being sold address the customers' immediate short-term needs, but the long-term and mid-term issues are not yet clearly represented, nor is customer trust fully established.

   - For 'Promising,' although the number of products is not high, the average value per order is significantly higher. This indicates that this segment has a strong trust in the store and brand, and there is a high likelihood they will become 'Best Customers' and 'Cannot Lose Them' in the future.

## Therefore, focusing on increasing Monetary (M) should be the top priority for the Marketing and Sales teams. A higher Monetary value also reflects a greater level of customer trust in the company.


