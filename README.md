# <b> Module-10-Challenge </b>

## <b> Crypto Clustering </b>

## --------

## Overview

The purpose of this assignment is to utilize traditional & unsupervised clustering techniques to find correlation patterns within a basket of cryptocurrencies across different
time period intervals.

## Features

The main tasks outlined in this project will include:

1.) Importing the required libraries and dependencies including scikit library & its required modules including KMeans, PCA & StandardScaler.  
<br>
2.) Importing the raw cryptocurrency price change data via the .csv files and convert into the appropriate dataframe and plotted to display the entire series of individual cryptocurrencies by type vs percent change. These individual cryptocurrencies are finally grouped and displayed by 'groupby' across expanding time intervals (i.e. 24h, 7d, 14d, etc.). (See Figure 1)
<br>
<br>
3.) Next, utilizing scikit-learn StandardScaler & fit_transform module, the original crypto dataframe is normalized in order to change the scale of the data (to enable easier normal distribution relationship presentation & linear relationship analysis) and the index is set by crypto coin id type.
<br>
<br>
4.) Furthermore, utilizing the scaled crypto dataframe created above, the data is next run through a KMeans algorithm model to determine an optimized value of k. This is done by plotting the inertia product (as a function of k-values from 1-10 based on the original dataframe percent change values). The optimal value is determined somewhat subjectively as the inertia product values are plotted as an 'elbow-curve' using hvplot as a function of each individual k-means value. (See Figure 2).
<br>
<br>
5.) The original scaled cryptocurrency dataframe data is then cluster according to the previously optimized k-means value. The 'predict' function of the KMeans module is used to predict the clusters of cryptocurrency and produce an array of predicted k-clusters. These are then plotted with x="price_change_percentage_24h" and y="price_change_percentage_7d" on an hvplot and each cluster color coded according to its cluster number. (See Figure 3).
<br>
<br>
6.) In the next section of the program, the scaled crypto dataframe undergoes a PCA (principal component analysis) basis of transformation to specifically reduce the number of features to n_components=3. Additionally, using the scikit-learn decomposition/pca module, the hierarchal explained variance ratio is calculated to determine that the first 3 pca components can account for 89.5% of the observed variance, and hence, be a sufficiently accurate representation of the objective data correlation fidelity. A new dataframe is also produced with the 3 pca dataset components representing every indexed row (coin id representation) in the original dataframe.
<br>
<br>
7.) Again, the elbow-curve derivation is used to determine the best value of k-means for the 3-component pca dataframe created above. Again, the relationship to the inertia values of the pca dataframe (as a function of each k-means value) are plotted for visual analysis. (See Figure 4).
<br>
<br>
8.) Finally, similarly as before, the original cryptocurrencies (using pca data reduction, this time) are scatter plotted using hvplot and clustered in groups according to the optimal k-means determined value using pca components. (See Figure 5).

## Data Analysis Results and Observations

### <u>Percent Change as a Function of Coin ID Grouped by Variable Time Interval</u>

<br>

<p align= "center" width="50">
    <img width= "50%" src="original_dataframe_coin_id_percent.png">
</p>
<i>Figure 1. Original dataframe imported from .csv file with percent change as a function of each individual cryptocurrency. Each is color coded grouped by its specific analyzed time interval.</i>

<br>

### <u>Elbow Curve for Inertia Values as Function of 'k'-Cluster Number - Original Data</u>

<br>

<p align= "center" width="50">
    <img width= "50%" src="elbow_curve_k_inertia_original.png">
</p>
<i>Figure 2. THe elbow curve is generated using original crypto dataframe values as inertia is a function of k-means. The largest rate of slope change is observed at the graph
vertex whereby k-mean=4.</i>

<br>

### <u>Plot of Price Change Percentage 24h vs. Price Change Percentage 7d - Original Data</u>

<br>

<p align= "center" width="50">
    <img width= "50%" src="percent_change_cluster_original.png">
</p>
<i>Figure 3. Plot of Price Change Percentage 24h vs. Price Change Percentage 7d - Original Data.</i>

<br>

### <u>Elbow Curve for Inertia Values as Function of 'k'-Cluster Number - PCA Component Data</u>

<br>

<p align= "center" width="50">
    <img width= "50%" src="elbow_curve_k_inertia_pca.png">
</p>
<i>Figure 4. Elbow Curve for Inertia Values as Function of 'k'-Cluster Number - PCA Component Data .</i>

<br>

### <u>Plot of PCA1 vs PCA2 Principal Component Relationship Based on the Predicted k=4 Cluster Number</u>

<br>

<p align= "center" width="50">
    <img width= "50%" src="percent_change_cluster_pca.png">
</p>
<i>Figure 5. Plot of PCA1 vs PCA2 Principal Component Relationship Based on the Predicted k=4 Cluster Number.</i>

<br>

#### <u>a.) SQL Table & Query Gens</u>

<br>

DROP TABLE IF EXISTS card_holder;
DROP TABLE IF EXISTS credit_card;
DROP TABLE IF EXISTS merchant_category;
DROP TABLE IF EXISTS merchant;
DROP TABLE IF EXISTS transaction;

CREATE TABLE card_holder (
id serial,
name character varying (50) NOT NULL,
PRIMARY KEY (id)
);

CREATE TABLE credit_card (
card VARCHAR (20) NOT NULL,
cardholder_id serial NOT NULL,
PRIMARY KEY (card),
FOREIGN KEY (cardholder_id) REFERENCES card_holder (id)
);

CREATE TABLE merchant_category (
id serial NOT NULL,
name VARCHAR (75) NOT NULL,
PRIMARY KEY (id)
);

CREATE TABLE merchant (
id serial NOT NULL,
name VARCHAR (100) NOT NULL,
id_merchant_category int NOT NULL,
PRIMARY KEY (id),
FOREIGN KEY (id_merchant_category) REFERENCES merchant_category (id)
);

CREATE TABLE transaction (
id int NOT NULL,
date TIMESTAMP NOT NULL,
amount float NOT NULL,
card VARCHAR(20) NOT NULL,
id_merchant int NOT NULL,
FOREIGN KEY (card) REFERENCES credit_card (card),
FOREIGN KEY (id_merchant) REFERENCES merchant (id)
);
<br>
<br>

#### <u>b.) Primary SQL Tables Foreign & Primary Key Join Query</u>

<br>

SELECT ch.id,
ch.name,
cc.cardholder_id,
cc.card,
t.amount,
t.date,
t.id_merchant,
m.name,
m.id_merchant_category,
mc.name
FROM card_holder as ch
JOIN credit_card as cc
ON ch.id = cc.cardholder_id
JOIN transaction as t
ON cc.card = t.card
JOIN merchant as m
ON t.id_merchant = m.id
JOIN merchant_category as mc
ON m.id_merchant_category = mc.id

<br>

### <u>Small Transaction (Amount < $2.00) Isolation & Analysis</u>

<br>
Initially, the first method chosen to isolate and analyze small transactions across all groups was via the SQL query method. This was done initially and the converted .csv files were looked at to see if there was any obvious outlying pattern shifts in spending habits. However, upon first inspection, it appeared to be questionable whether purchase price discrepancies actually showed any correlation to fraudulent behavior (i.e. such as micro transactions made in quick succession after initial purchases, out of habit spending patterns, or outliers that could definitely be considered suspicious). Credit card transactions in general can have wide spread in price/amount. As well, defining outliers does not in fact take into consideration the randomization of assumed purchase behavior from the customer and/or merchant. However, outliers were analyzed and isolated with a few different methods.

<br>
The primary method used was to convert the main linked SQL database tables via primary/foreign keys into a workable database, filtered via 'Amount' less than $2.00 and then using Holoviews Plots (hvPlot), put into a workable scatter plot that was 'groupby' sorted via 'Cardholder ID'. This way, you had a quick, workable, & visual graph to look at any
obvious major outliers or obvious pattern shifts in 'Amount' expenditures.

<br>
By going through the drop-down table, based on Cardholder ID, it may warrant further analysis to look at the card activate with sporadic and non-randomized 'consistent' purchases. A few cards that hold potential of some fraudulent activity may include Cardholder ID 3, 6, 9, 17. Cardholder 10 also shows some patterns of potential strange transactional values between $1.80 and $1.95 at both pubs at restaurants, but nothing concrete. This is the same with Cardholder 18 (tight range of possibly curious micro transactions at pubs, food trucks and restaurants - (i.e. pub at 6:13am in the morning). Again, this is speculation and Cardholders should be notified to confirm.

Additionally, in terms of top 5 merchants that could potentially be prone to small transaction fraud are included in a series list (see visual_data_analysis.ipynb). They are:
i.) Wood-Ramirez (7), Baker Inc. (6), Hood-Phillips (6), Walker-Deleon and Wolf (5) and Atkinson Ltd. (5).

<br>

### <u>100 Highest Transactions Made Between 7:00 am and 9:00 am</u>

<br>
Next, an analysis was performed on the 100 highest transactions made between 7:00 am and 9:00 am. Here, we see there is more obvious major outliers - and by linking primary & foreign keys to link all data associations, we can see there is very possible evidence of credit card fraud in this case. Utilizing an interactive scatter plot to display all column data on each scatter point, there is evidence that Cardholder's 1, 9, 16, & 25 have been victim's of credit card fraud. Or, at the very least, warrant immediate further investigation. This was determined by high transaction amounts over $1000 in nature at merchant categories that included restaurants, bars and coffee shops. (I.e. Cardholder ID 9 [Credit Card ####3340] has apparently spent $1009 at a coffee shop at 7:41 am). This is without a doubt high probable card fraud. It is possible fraud occurs mainly during this interval in the morning as a result of people not paying attention, rushing to work and having less personal self-awareness to their outward environment or that it's a period when most people are not concerned with checking their transaction statements. (See Figure 2 below).

<br>

<p align= "center" width="50">
    <img width= "50%" src="fraud_detection_01_top100.png">
</p>

### <u>Potential Credit Card Fraud With Cardholder IDs 2 & 18</u>

<br>

Again, it appears highly probable that both Cardholder ID 2 & 18 have been subjected to card fraud, similar to those described above. Again, visual data graphing suggests there is high frequency and consistent restaurant, pub & coffee shop transactions occurring with totals between $1.00-$20.00. However, between both card holders, there are a total of 10 major outliers between the two - some transactions in excess of $1000. These seem highly unusual and should be investigated immediately - up to and including having the cards frozen. (See Figure 3 below).

<br>

<p align= "center" width="50">
    <img width= "50%" src="fraud_detection_cardholder2_and_18.png">
</p>

<br>

### <u>Potential Corporate Credit Card Fraud With Cardholder ID 25</u>

<br>

Again, it appears highly probable that both Cardholder ID 25 has been subjected to card fraud, similar to 2 & 18 described above. There are a total of outlier transactions that appear to have totalled over $1000 and are departed significantly from their 1-3 standard deviations and mean values. This card should be halted immediately and cardholder 25 contacted. (See Figure 4 & 5 below).

<p align= "center" width="50">
    <img width= "50%" src="fraud_detection_cardholder_25.png">
</p>

<br>

<p align= "center" width="50">
    <img width= "50%" src="fraud_detection_cardholder_25_months.png">
</p>

<br>

## Challenge File(s)

\*Note: Refer to the challenge file concerning the outlier analysis using statistical analysis methods and quartile analysis and their results.

## Running the Project

Running the project can be accomplished by accessing the https://github.com/KristopherGit/Module-7-Challenge.git Git Repository and running each section sequentially.

## Dependencies

No other outside resources are required to run the project except a python engine / python program and the following imported libraries and modules:

import pandas as pd
import hvplot.pandas
from pathlib import Path
from sklearn.cluster import KMeans
from sklearn.decomposition import PCA
from sklearn.preprocessing import StandardScaler

## Contributors

C Ringwood
