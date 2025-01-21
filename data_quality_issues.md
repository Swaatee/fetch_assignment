# Data Quality issues

## 1. Users
>1. Missing values

|Column|Count|
|---|---|
|birth_date|3675|
|state|4812|
|language|30508|
|gender|5892|



## 2. Products
>1. Barcodes are the foreign key to transactions table and there are missing barcodes for `4025` rows out of total `845552` rows. 
>2. There are multiple instances where same barcode is used for products from different manufacturer or brands or both
>3. Duplicated rows in table
```
Health & Wellness,Hair Care,Hair Color,HENKEL,SCHWARZKOPF,052336919068 
Health & Wellness,Hair Care,Hair Color,HENKEL,GÖT2B,052336919068
Health & Wellness,Hair Care,Hair Color,HENKEL,SCHWARZKOPF,017000329260
Health & Wellness,Hair Care,Hair Color,HENKEL,GÖT2B,017000329260
```

>3. Missing values by column

|Column|Count|
|---|---|
|Category_1|111|
|Category_2|1424|
|Category_3|60566|
|Category_4|778093|
|Manufacturer|226474|
|Brand|226472|

>4. Barcode is Integer type in ER diagram but some db might fail to parse it as Integer as it's value exceeds Integer capacity, so it should be BigInt


## 3. Transactions
>1. Barcode is Integer type in ER diagram but some db might fail to parse it as Integer as it's value exceeds Integer capacity, so it should be BigInt. Missing `barcode` which is foreign key to products table, there are `5762` rows with missing barcode out of total `50000` transactions.
```
SELECT count(*) from transactions where barcode=''
```
>2. There seems to be only `262` transactions for which user info is available in ***users*** table. This means there is lot of user info missing.
```
select count(*) from transactions where user_id in (select DISTINCT(id) from users)
```
>3. There seems to be only 60% transactions for which product info is available in ***products*** table. This means there is lot of product info missing.
```
select count(*) from transactions where user_id in (select DISTINCT(id) from users)
```
>4. A transaction identied by a `receipt_id` can have multiple products listed where number of each product type is suppoed to be grouped as `quantity` and total sale value as `sale`, however it's not clear how many quantity of product of a given barcode type was sold for how much sale value as sometimes `quatity` is 0 or `sale` is 0. Additionally, there are numerous duplicate rows for the same combination of `receipt_id`, `barcode`, and other attributes, while in some cases, they are aggregated under the quantity field. Take a look at below example for 3 receipts. 
```
SELECT count(*), receipt_id,barcode from transactions where barcode !='' group by receipt_id,barcode order by count(*) desc
select * from transactions where receipt_id in ('bedac253-2256-461b-96af-267748e6cecf','00d9009e-e275-4a9d-92c6-85ef2e80fffe','2acd7e8d-37df-4e51-8ee5-9a9c8c1d9711') order by receipt_id desc
```
|receipt_id|purchase_date|scan_date|store_name|user_id|barcode|quantity|sale|
|---|---|---|---|---|---|---|---|
|bedac253-2256-461b-96af-267748e6cecf|2024-09-08 00:00:00|2024-09-08 20:00:42|KROGER|614f7e8081627974a57c8a9e|011110121141|0.00|1.00|
|bedac253-2256-461b-96af-267748e6cecf|2024-09-08 00:00:00|2024-09-08 20:00:42|KROGER|614f7e8081627974a57c8a9e|011110121141|1.00|0.00|
|bedac253-2256-461b-96af-267748e6cecf|2024-09-08 00:00:00|2024-09-08 20:00:42|KROGER|614f7e8081627974a57c8a9e|011110121141|0.00|1.00|
|bedac253-2256-461b-96af-267748e6cecf|2024-09-08 00:00:00|2024-09-08 20:00:42|KROGER|614f7e8081627974a57c8a9e|011110121141|1.00|0.00|
|bedac253-2256-461b-96af-267748e6cecf|2024-09-08 00:00:00|2024-09-08 20:00:42|KROGER|614f7e8081627974a57c8a9e|011110121141|0.00|1.00|
|bedac253-2256-461b-96af-267748e6cecf|2024-09-08 00:00:00|2024-09-08 20:00:42|KROGER|614f7e8081627974a57c8a9e|011110121141|1.00|0.00|
|bedac253-2256-461b-96af-267748e6cecf|2024-09-08 00:00:00|2024-09-08 20:00:42|KROGER|614f7e8081627974a57c8a9e|011110121141|1.00|1.00|
|bedac253-2256-461b-96af-267748e6cecf|2024-09-08 00:00:00|2024-09-08 20:00:42|KROGER|614f7e8081627974a57c8a9e|011110121141|1.00|1.00|
|bedac253-2256-461b-96af-267748e6cecf|2024-09-08 00:00:00|2024-09-08 20:00:42|KROGER|614f7e8081627974a57c8a9e|011110121141|1.00|1.00|
|bedac253-2256-461b-96af-267748e6cecf|2024-09-08 00:00:00|2024-09-08 20:00:42|KROGER|614f7e8081627974a57c8a9e|011110121141|1.00|1.00|
|bedac253-2256-461b-96af-267748e6cecf|2024-09-08 00:00:00|2024-09-08 20:00:42|KROGER|614f7e8081627974a57c8a9e|011110121141|1.00|1.00|
|bedac253-2256-461b-96af-267748e6cecf|2024-09-08 00:00:00|2024-09-08 20:00:42|KROGER|614f7e8081627974a57c8a9e|011110121141|1.00|1.00|
|---|---|---|---|---|---|---|---|
|2acd7e8d-37df-4e51-8ee5-9a9c8c1d9711|2024-09-08 00:00:00|2024-09-08 11:13:02|WALMART|663140f9b7b24d45d938f3be|024000048336|1.00|0.00|
|2acd7e8d-37df-4e51-8ee5-9a9c8c1d9711|2024-09-08 00:00:00|2024-09-08 11:13:02|WALMART|663140f9b7b24d45d938f3be|024000048336|0.00|1.36|
|2acd7e8d-37df-4e51-8ee5-9a9c8c1d9711|2024-09-08 00:00:00|2024-09-08 11:13:02|WALMART|663140f9b7b24d45d938f3be|024000048336|1.00|0.00|
|2acd7e8d-37df-4e51-8ee5-9a9c8c1d9711|2024-09-08 00:00:00|2024-09-08 11:13:02|WALMART|663140f9b7b24d45d938f3be|024000048336|0.00|1.48|
|2acd7e8d-37df-4e51-8ee5-9a9c8c1d9711|2024-09-08 00:00:00|2024-09-08 11:13:02|WALMART|663140f9b7b24d45d938f3be|024000048336|1.00|1.48|
|2acd7e8d-37df-4e51-8ee5-9a9c8c1d9711|2024-09-08 00:00:00|2024-09-08 11:13:02|WALMART|663140f9b7b24d45d938f3be|024000048336|1.00|1.48|
|2acd7e8d-37df-4e51-8ee5-9a9c8c1d9711|2024-09-08 00:00:00|2024-09-08 11:13:02|WALMART|663140f9b7b24d45d938f3be|024000048336|1.00|1.36|
|2acd7e8d-37df-4e51-8ee5-9a9c8c1d9711|2024-09-08 00:00:00|2024-09-08 11:13:02|WALMART|663140f9b7b24d45d938f3be|024000048336|1.00|2.28|
|---|---|---|---|---|---|---|---|
|00d9009e-e275-4a9d-92c6-85ef2e80fffe|2024-09-03 00:00:00|2024-09-03 18:58:48|WALGREENS|5f1e251458dc8c148b24dd8f|300450488282|2.00|0.00|
|00d9009e-e275-4a9d-92c6-85ef2e80fffe|2024-09-03 00:00:00|2024-09-03 18:58:48|WALGREENS|5f1e251458dc8c148b24dd8f|300450488282|2.00|23.98|
