Summary
Fraud Landscape Overview of Novapay
Novapay operates in a high-growth, high-risk digital payment ecosystem. As a global money transfer company processing real-time transactions, it faces diverse fraud threats across multiple currencies, payment channels and jurisdictions. The existing rule-based fraud detection system is increasingly insufficient against the sophisticated, adaptive fraud tactics.
Fraud Type Identified
Fraud Type	Description	Impact on Novapay
Identity Theft	Fraudsters use stolen credentials to impersonate legitimate customers and initiate unauthorised transactions	Financial loss, reputational damage, and regulatory penalties
Unauthorised Payment 	Transactions initiated without the account holder's consent, often through compromised devices	Direct financial loss, chargeback, and costs lead to customer churn
Account Takeover	Full control of legitimate accounts obtained through credential theft or social engineering	Significant financial exposure, erosion of trust
Synthetic Fraud 	Creation of fake identities using a combination of real and fabricated information	New account fraud, difficulty in detection due to the absence of a historical pattern

Success Criteria
Criterion	Target	Measurement
Fraud Detection Recall	>15% improvement over rule-based baseline	(True Positives) / (True Positives + False Negatives)
False Positive Rate 	Reduction by >20% from the current rate 	(False Positive) / (Total Legitimate Transactions)
Precision 	Maintain > 85 to ensure review efficiency	(True Positives) / (True Positives + False Positives)
AUC-ROC	>0.95, indicating excellent discrimination	Area Under the Receiver Operating Curve
Real-Time Detection	<300ms per transaction scoring	System response time measurement
Model Explainability	SHAP reports available for each prediction	Evidence of interpretability for regulatory audit
Scalability	Support 2-3x current transaction volume	Load testing during peak periods
Customer Experience	No increase in Transaction decline rate due to false positives	Comparison of false positive rates pre- and post-implementation



Data Assessment Report of Novapay
1 Data Overview
Aspect	Details
Dataset	11,400 transactions, 26 columns
Transaction Types	Mobile, Web, ATM, POS with inconsistent casing
Currencies 	In Different denominations
Label source (Target Variable)	Is\_fraud (0/1) – binary classification

2 Label Distribution
Metric 	Value
Total Transaction 	11,400
Fraudulent Transaction	997,    8.7%
Non-fraudulent transactions	10,403,    91.3%
Fraud rate: 8.7% of all transactions, suggesting high class imbalance
3 Schema and Key Fields
Field 	Purpose	Quality
Transaction\_id	Unique Identifier	ok
Customer\_id	User\_ Identifier	ok
Time\_stamp	Transaction\_Time	Not ok, in object data type
Amount\_src	Original  Amount	ok
Amount\_usd	USD equivalent	Missing Values
Channel	Payment channel	Inconsistent casing (formatting issue)
Is\_fraud	Fraud label	Available
fee	Transaction fee	Negative value and missing values
Ip\_risk\_Score	IP risk indicator	Values greater > 1(invalid)
Device\_Trust\_Score	Device Trust	Negative values and missing value
KYC\_tier	User Verification Level	Inconsistent formatting and missing values

4 Label Reliability and Bias Risks
Risk area	Description	Mitigation
Class Imbalance	Only 997 fraud cases, models will likely predict “Non-fraud” for all 	Apply SMOTE, ADASYN, cost-sensitive learning, or Ensemble resampling
Proxy Discrimination	IP\_country, Location mismatch, KYC\_tier may correlate with protected attribute	Audit features for proxy bias; better to remove sensitive proxies
Chargeback delays 	Some fraud may be confirmed after initial flagging, causing mislabelling 	Incorporate chargeback data, if possible
Missing Feature values	Ip\_risk\_score, device \_trust\_score missing for many rows 	Impute with median or mean, depending on the skewness or use as a missing indicator

5 Recommendations
Priority	Actions
Critical	Handle class imbalance using (SMOTE)
Critical	Fix invalid time stamps and negative  value in preprocessing
High	Standardise Categorical variables (channel if possible or ideal and KYC\_tier)
High	Evaluate potential proxy bias (location mismatch)
medium	Consider fairness metrics (equal opportunity, statistical parity)
Medium 	Document label reliability and chargeback timing



Report

The LightGBM + SMOTE model stands out among all other models tested, although the baseline results were better, but we failed to report them. We wouldn’t recommend it for deployment, as our dataset used in building the models portrays data imbalances which were not addressed in the baseline models.

The LightGBM + SMOTE model demonstrates excellent fraud detection capabilities with a 99% accuracy rate, successfully identifying 90% of all fraud cases while maintaining a 98.8% precision rate. The model was tested on a time-based split (70 older data/30 newer data), ensuring robust performance on future transactions.

Metric	Value	Interpretation

Accuracy	99%	Correctly classifies 99 out of 100 transactions

Recall	89.98%	Catches 90% of all actual fraud cases

Precision	98.78%	When 100 transactions are flagged, 98.8 of them are truly fraudulent.

F1-score	94.17%	Creating a balance between precision and recall, which shows a strong balance between both

ROC-AUC	96.72%	Excellent discrimination between fraud and legitimate 





Model Performance

Confusion Matrix on 3361

&#x09;Non-Fraud	Fraud

Predicted non-fraud	2902 correct prediction of non-fraud	45 missed frauds (false negatives)

Predicted	10 (False positive)	404 (Correct Fraud)



Catching 404 out of 449 frauds with only 10 false positives.

Global Feature Importance

Global Importance measures feature importance across all transactions in the dataset. It shows, on average, across all fraud and legitimate transactions, which features are the most powerful predictors of fraud.

Top 10 Global Features

Rank	Features	Gain	Cumulative	Business

1	Combined risk	43,041	64.21%	Dominant feature -high

2	Exchanged rate source to distance	4,638	71.1%	Detect unusual currency movement - high

3	Account age years	3,2877	76%	New accounts are risky - high

4	Device trust score	2,864	80.3%	Device reputation matters - high

5	Corridor risk	1,731	82.9%	Geographic risk - medium

6	KYC tier encoded	1,673	85.4%	Verification status - medium

7	Risk scores internal	1,673	87.8%	Internal scoring - medium

8	Txn velocity 24h	586		88.7%	Transaction frequency - low

9	Txn velocity 1h	388	89.3%	Rapid transaction - low

10	device	303	89.7%	Device risk -low



Key Insights

Insight	Explanation

Combined risk	64.2% of all predictive power, which is over 10x more than the 2nd best.

Top 5 features	Account for 82.9% of fraud detection capability

Risk scoring matters	Combined risk + risk score internal = 66.6%

Transaction features	Exchange rate + velocity = 8.5%

Customers features	Account age + KYC = 7.4%



Local Feature Importance (SHAP)

Local importance (SHAP value) measures feature impact for a SINGLE transaction. It shows for a specific transaction, which features caused the fraud flag?



&#x20;

Why not flagged: Despite some risk indicators (high amount, new account), the combined risk is very low (0), preventing the transaction from crossing the fraud threshold.



&#x20;

High risk corridor + high amount + brand new account (4 days) + untrusted device = fraud flag



&#x20;



Even Higher risk corridor + very high amount + even newer account (3 days) + very low trust device = clear fraud



&#x20;

High-risk corridor + high amount + new amount new account (4 days) + untrusted device = consistent Pattern











Global vs Local comparison

&#x20;



Feature Importance Comparison

Feature	Global Importance (Gain)	Local Impact (Flagged Transactions)	Insight

Combined risk	43,041	Very low (Not in top 5)	Powerful globally, but not the trigger

Corridor risk	1,731	+5.5 to +6.0	The fraud trigger!

High amount	Not in the top 10	+0.96 to1.0	Drives flagged transactions

Device low trust	303	+0.70 to 0.82	Consistent fraud indicator

Account age days	Not in the top 10	+0.31 to + 0.38	New accounts = fraud risk



While global tells regulators that the model is built on robust features that consistently separate fraud from legitimate transactions, local tells regulators exactly why each transaction was flagged, preventing arbitrary or black box decisions.

Business Impact

Fraud Prevention (Revenue Protection)

Some Assumptions:

1\.	 Annual transaction volume: 100,000

2\.	Fraud rate: 8.88% (from the full dataset)

3\.	Average fraud transaction value: $500



Scenario	Value

Annual Transaction Attempts 	8,880 (100,000 \*8.88%) 

Fraud caught (90%)	7992

Fraud missed (10%)	888

False Positives (0.3%)	273



Financial Impact

Scenario ($500 Average Fraud Value)

Metric	Without Model	With Model	Savings

Fraud Losses	$4,440,000	$444,000	$3,996,000

Review costs	$44,000	$41,325	$2,675

Total Cost	$4,484,400	$485,325	$3,998,675





