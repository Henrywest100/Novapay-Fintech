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
Label source (Target Variable)	Is_fraud (0/1) – binary classification

2 Label Distribution
Metric 	Value
Total Transaction 	11,400
Fraudulent Transaction	997,    8.7%
Non-fraudulent transactions	10,403,    91.3%
Fraud rate: 8.7% of all transactions, suggesting high class imbalance
3 Schema and Key Fields
Field 	Purpose	Quality
Transaction_id	Unique Identifier	ok
Customer_id	User_ Identifier	ok
Time_stamp	Transaction_Time	Not ok, in object data type
Amount_src	Original  Amount	ok
Amount_usd	USD equivalent	Missing Values
Channel	Payment channel	Inconsistent casing (formatting issue)
Is_fraud	Fraud label	Available
fee	Transaction fee	Negative value and missing values
Ip_risk_Score	IP risk indicator	Values greater > 1(invalid)
Device_Trust_Score	Device Trust	Negative values and missing value
KYC_tier	User Verification Level	Inconsistent formatting and missing values

4 Label Reliability and Bias Risks
Risk area	Description	Mitigation
Class Imbalance	Only 997 fraud cases, models will likely predict “Non-fraud” for all 	Apply SMOTE, ADASYN, cost-sensitive learning, or Ensemble resampling 
Proxy Discrimination	IP_country, Location mismatch, KYC_tier may correlate with protected attribute	Audit features for proxy bias; better to remove sensitive proxies
Chargeback delays 	Some fraud may be confirmed after initial flagging, causing mislabelling 	Incorporate chargeback data, if possible
Missing Feature values	Ip_risk_score, device _trust_score missing for many rows 	Impute with median or mean, depending on the skewness or use as a missing indicator

5 Recommendations
Priority	Actions
Critical	Handle class imbalance using (SMOTE)
Critical	Fix invalid time stamps and negative  value in preprocessing
High	Standardise Categorical variables (channel if possible or ideal and KYC_tier)
High	Evaluate potential proxy bias (location mismatch)
medium	Consider fairness metrics (equal opportunity, statistical parity)
Medium 	Document label reliability and chargeback timing  

