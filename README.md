# MRP
### TMU MRP work

 *** FOR MORE DETAIL AND FIGURE, LINK TO A SEPARATE POST WILL BE POSTED LATER***

Since the dataset contains text, date, and numeric fields, I conducted two different kinds of analysis to learn more about my dataset. 

# 1.	Data Acquisition
   
The labelled addresses are obtained from Kaggle published dataset [2], which is a taken from a trusted service called EtherScan. Google BigQuery is also used to obtain all relevant information, for instance transactions detail of each transaction for each address [3]. 

The detail code is provide as followed in figure 2, where the “kaggle_table” is the table containing addresses from the Kaggle public dataset.

Due to the limitation of a local machine, I cannot use the full 20,000 addresses as the file size with full transaction data is too big. However, 10,000 addresses already yield over 10GB of data with 17,000,000+ transactions. Due to local computational constraints, I am limited to only 4,422 addresses for Machine Learning models training. These 4,422 addresses have a total of 7,660,789 transactions. It was then combined with other feature-engineering parameters (table 1) to create a 5GB csv file. It has to be noted that we aim to categorize transactions even for accounts with zero or few histories, so the Ethereum address column is removed.



# 2.	Data Processing
   
As a result of using public dataset for big data, Google BigQuery breaks returned data into 450MB chunks. Hence, we have a total of 23 files to merge. It has to be noted that as mentioned above, it is vital to keep the ratio as close to real life as possible. Thus, by taking only bad activities in the first 10 files, we retained 211,693 instances of ‘Dodgy’ class and 7,449,093 instances of 'Legit' ones, making the ratio 2.8 to 97.2 that satisfied the requirements.

After these steps, there are many transactions with nan or empty values in many columns. Thus, those transactions were removed. Then, looking at the number of tags, there are obviously some noisy tags. In fact, the average occurrences stand at 30,643 times. Those tags at the bottom 5th percentiles were treated as noise since they did not even occur for more than 10 times in this dataset. 

Next, as we have mixed types of data, it is required to convert categories column with encoder to have a standard all-float type dataframe. Here, we used one-hot encoding because it is independent and does not make assumption of the order of categories like ordinal encoding. Moreover, XGBoost and LightGBM are based on decision tree, which handles the label encoding method well. Then, we proceeded to scale columns using the MinMax Scalar library by the Sklearn package. However, there are two sets of dataframe is saved here with one keeping the date format of columns like block timestamp for descriptive analysis purpose while the other will also be scaled to prepare as input for ML models. 



# 3.	Descriptive Analysis
   
Moving to discovery analysis part of this dataset, it revolves around the distinct patterns that emerge when comparing legitimate and dodgy transactions. The data, spanning from 2015 to 2023, indicates that the majority of transactions are conducted by smart contracts, with a substantial portion being legitimate. However, due to limitation of using free source, I could not get an even time distribution as the data is skewed to the 2018-2022 range (figure 4). Furthermore, the illegal activities are up to only 2019 (figure 3) and it consequently misses out a huge portion of state-sponsored cyber attack transactions that became popular from 2022. Given more financial resources, the premium pro plus package from Etherscan could help with a more even distribution and latest data.

Notably, as mentioned above, the dominance of the "Legit" class is creditable as it is supported by Chain Analysis. It also has to be noted that the majority of transactions are made by smart contract service and not wallet. While this gives more confidence in detecting user dodgy transactions, it can potentially miss out systemic scam crypto projects or schemes like FTX.
The data also incorporates the time zone, specifically UTC–0 (England), providing a global perspective on Ethereum transactions. Thanks to its nature, Ethereum can help process cross-border transactions faster with less fees than traditional means. Based on figure 5 and figure 6, it can be seen that there is clear distinction between legit and dodgy transactions.


In fact, there is a significant drop in dodgy activities volume before 1:00 (21:00 UTC-4 Toronto time, 8:00 UTC+7 Bangkok time) and after 15:00 UTC-0 (11:00 UTC-4, 22:00 UTC+7). This is not the same case for legit transactions, except for a spike at 8:00 to 9:00 UTC-0 (4:00 to 5:00 UTC-4, 15:00 to 16:00 UTC +7). This suggests that there is a correlation between working hours of East Asians and evening time of North America with dodgy transactions.

It can also be seen that legit crypto trades are done during the weekend as Saturday and Sunday volumes take the top two (figure 8). On the other hand, bad actors seem to favor Friday, Saturday and surprisingly Tuesday (figure 7). This suggested that cryptocurrencies are treated as past-time investment rather than a full-time job likes stock or bond traders.



# 4. Result
   
The outcome of this paper falls into the selection of suitable models. For a crypto investigation scenario, Multi-Layer Perceptron (MLP) proves to be a superior choice due to its speed and effective performance. It not only provides robust results with only one round of training, but also outshines other models when trained for a few more rounds. The low training time, in addition to its performance which is on par with, if not superior to, XGBoost with five rounds, further solidifies MLP as the go-to model. 

However, when it comes to live monitoring, the situation is slightly more complex. The decision between the MLP and LSTM models hinges on the specific requirements of the scenario. While MLP may lead the way initially, LSTM's rate of improvement is substantial and it pulls ahead by round three. Nevertheless, this performance comes at the cost of increased learning time. Hence, the choice of model for live monitoring in cryptocurrency exchanges needs careful consideration.

