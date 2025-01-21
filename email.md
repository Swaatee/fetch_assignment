Hi,

Hope you're all doing well. I have been working with Fetch datasets of users, transactions and products and have few interesting finds that I'd like to share. I have found some major data quality issues that should be addressed along with some interesting trends that may help making data driven decisions.

Data quality issues: 
1. DB tables users,transactions and products have lot of missing values.
2. Out of all transactions we only have user info for ~260 transactions. This data is important to associate user trends for purchases.
3. Similarly, products table have only 60% information of all products sold in transactions. This is another gap to associate products sale trends/
4. Transactions and products table have a ton of duplicated entries, which indicate issue in the system adding entries to the database. We should look into how many places is the data being added from which ideally should be only one system.
5. Transactions table seems not following a standard regarding storing information about how many number of a particular product was sold in on transaction. This is hampering data quality. It could be a result of duplicated entries. Would be nice to connect with the SME to understand this better to resolve confusion. Let me know who I can reach out to follow up on this.
6. Lastly, a lot of products(~27) have same barcodes while their brands and manufacturer are different. Would like to understand this better too if this is intended, seems like a data quality issue.

Addressing these data quality issues would be great step forward. There are some interesting insights as well that I'd like to share:


