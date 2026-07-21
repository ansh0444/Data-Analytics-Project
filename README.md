# ElectroHub - Dashboard

### Dashboard Link : https://app.powerbi.com/view?r=eyJrIjoiYzFkMTU1ODItNGI2NC00M2Q4LTgxZWYtNGUxMDM3YzYyNDBkIiwidCI6ImI0ODUyNjAyLWM5Y2ItNDNmZi04ZmUwLWFhYzMzODAyZDFlNSJ9&pageName=395e7363601fe2f75886

## Problem Statement

This dashboard helps ElectroHub understand its sales performance, profitability, and customer purchasing behavior across different product categories, cities, and time periods. It enables the business to identify its best and worst-performing products based on sales, profit, and quantity sold, helping management make informed inventory and marketing decisions.

Through interactive visualizations, the dashboard provides insights into sales trends over daily, monthly, quarterly, and yearly periods, while also showing the relationship between sales and profit. It allows users to compare business performance between any two selected periods and analyze the impact of discounts on overall sales and profitability.

The dashboard also displays key business metrics such as total orders, average discounts by discount category, and detailed order-level information that can be filtered by product, date, customer ID, and promotion category. Additionally, it presents city-wise sales performance to identify high-performing markets and areas that require improvement.

Overall, this dashboard enables management to monitor business performance effectively, identify growth opportunities, optimize pricing and promotional strategies, and make data-driven decisions to improve revenue and profitability.


### Steps followed 

- Step 1 : Load data into Power BI Desktop, dataset is a csv file.
- Step 2 : Open power query editor & in view tab under Data preview section, check "column distribution", "column quality" & "column profile" options.
- Step 3 : Also since by default, profile will be opened only for 1000 rows so you need to select "column profiling based on entire dataset".
- Step 4 : It was observed that in none of the columns errors & empty values were present except column named "Price Per Unit,Total Sales, Discount Percentage,Discount Value,Net Sales" with null values.
- Step 5 : Create a new Percentage column using Conditional Column in Power Query. Convert the text values in the Price Reduction Type column into numeric percentages (e.g., 20% off → 20, 10% off → 10, 50% off → 50, 70% off → 70, and Buy 1 Get 1 Free → 50). Finally, change the new Percentage column's data type to Whole Number for accurate calculations and dashboard visualizations. 
- Step 6 : During data transformation, certain columns contained only 0 values and null values. These values were corrected by merging related tables using Merge Queries in Power Query to fetch the required information. Furthermore, Custom Columns were created to derive calculated fields including Total Sales, Discount Value, Net Sales, and Profit, making the dataset suitable for reporting and analysis.
- Step 7 : A Star Schema data model was created by establishing One-to-Many (1:*) cardinality relationships between the Fact Table and the Dimension Tables (Dim Product, Dim Customers, Dim Promotion, and Date Table). The Fact Table was placed at the center of the model, while the dimension tables were connected using their respective primary and foreign keys. A separate Measure Table was also created to store all DAX measures and was intentionally left without any relationships, as it is used only for organizing calculations.Using a Star Schema improves data model performance, simplifies DAX calculations, and enables faster filtering, querying, and easier dashboard maintenance.
![StarSchema](https://github.com/ansh0444/Data-Analytics-Project/blob/588c0c1cf89a3ff1fd3153b0d538ae36ada0e0a6/img/image.png)
- Step 8 : I have used 2 different approach for Requirement( Comapring the Sales,Profit and Quantity sold between any two periods select by user).

    - Step 8.1: Since the report requires comparison of business performance between two different time periods, "two Date Tables" and "two Date Slicers" were created. "Date Table 1" was connected to the Fact Table through an "active relationship", while "Date Table 2" used an "inactive relationship". DAX measures with the "USERELATIONSHIP()" function were created to activate the inactive relationship for comparison visuals, enabling "Clustered Column Charts" to compare "Total Sales", "Total Profit", and "Quantity Sold" across two independently selected periods.

    Following DAX expression was written for the same,

            Sum of Net Sales = CALCULATE(SUM('Fact Table'[Net Sales]),ALL('Date Table 1'),USERELATIONSHIP('Date Table 2'[Date],'Fact Table'[Date (dd/mm/yyyy)]))

            Total Profit = CALCULATE(SUM('Fact Table'[Profit]),ALL('Date Table 1'),USERELATIONSHIP('Date Table 2'[Date],'Fact Table'[Date (dd/mm/yyyy)]))

            Units Sold = CALCULATE(SUM('Fact Table'[Units Sold]),ALL('Date Table 1'),USERELATIONSHIP('Date Table 2'[Date],'Fact Table'[Date (dd/mm/yyyy)]))

    - Step 8.2: Since the report requires comparison of business performance between two different time periods, Edit Interactions was used to configure the Date Slicers. This ensured that each slicer interacted only with its respective visuals, allowing users to independently filter and compare Total Sales, Total Profit, and Quantity Sold without affecting the other comparison visuals.

- Step 9 : A "Clustered Bar Chart" was added to the report design area representing the "Average Discount Value by Promotion Name". While creating this visual, a "visual-level filter" was applied from the "Filters" pane to exclude "blank Promotion Name" values, ensuring that the chart displays only valid promotions and their corresponding average discount values..
           
            Although, by default, while calculating average, blank values are ignored.
- Step 10 : A "Scatter Chart" was added to the report design area representing the relationship between "Profit" and "Net Sales". While creating this visual, the "Profit" field was added to the "X-axis" and "Net Sales" was added to the "Y-axis", allowing users to analyze the correlation between profitability and sales performance.
- Step 11 : A "Line Chart" was added to visualize the "Sales Trend by Period", enabling users to monitor daily sales performance over time and identify seasonal patterns, peaks, and fluctuations.

            This visual changes dynamically when different visual filters are applied, enabling users to monitor sales trends across selected time periods.
 
In our dataset, Some parameters were assigned value 0, representing those parameters are not applicable for some customers.

All these values have been ignored while calculating average rating for each of the parameters mentioned above.

- Step 12 : A "Matrix visual" was added to display "Net Sales by City", enabling users to compare city-wise sales performance and identify the highest and lowest revenue-generating cities.
- Step 13 : A "Card visual" was added to the report to display the "Total Number of Orders", providing a quick overview of the total orders processed. 
- Step 14 : Six "Stacked Bar Charts" were added to the report design area representing the "Top 5" and "Bottom 5" Products based on "Sales", "Profit", and "Quantity Sold". While creating these visuals, the "Top N" filter was applied from the "Filters" pane to display the "Top 5" and "Bottom 5" products by setting the required measure and limiting the number of displayed products to "5".

            These visuals change dynamically when different visual filters are applied, enabling users to identify the highest and lowest performing products across the selected business metrics.

- Step 15 : A "Table Visual" was added to the report design area to display "Sales", "Profit", "Discount Value", "Net Sales", and all remaining order-level fields for each transaction. "Visual Filters (Slicers)" were added for "Date", "Product Name", "Customer Name", and "Promotion Name" to enable users to filter the table dynamically. A DAX measure named Sum Di was created and applied as a "visual-level filter" in each slicer to ensure that only records with valid "Net Sales" values are displayed.

Following DAX expression was written for the same,

              Sum Di = SUM('Fact Table'[Net Sales])
        
Although, by default, slicers display all available values, the Sum Di measure was used to remove items with no corresponding transaction data, ensuring that only relevant filter values are shown.


 
 - Step 16 : The report was then published to Power BI Service.
 
 
![Publish_Message](https://github.com/ansh0444/Data-Analytics-Project/blob/1d26c9b24b87493a022c89290d4b339dc6d592b6/img/publish.png)

# Snapshot of Dashboard (Power BI Service)

![dashboard_snapo](https://github.com/ansh0444/Data-Analytics-Project/blob/d056567552219b4122bbe479cbf517b099852c57/img/EDA1.png)

![dashboard_snapo](https://github.com/ansh0444/Data-Analytics-Project/blob/a8e8688c939e244f100c5e264c38ac7176668dcc/img/EDA2.png)

![dashboard_snapo](https://private-user-images.githubusercontent.com/121956665/617088834-05936037-82e0-43d1-9f0c-84dc71721be1.png?jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3ODMxNjIxODksIm5iZiI6MTc4MzE2MTg4OSwicGF0aCI6Ii8xMjE5NTY2NjUvNjE3MDg4ODM0LTA1OTM2MDM3LTgyZTAtNDNkMS05ZjBjLTg0ZGM3MTcyMWJlMS5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjYwNzA0JTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI2MDcwNFQxMDQ0NDlaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT1iYjliYWEyNGRiMWRlMDViYTU1NDllYWY5OTMwYTFiNTI5NDQ3YWIxZTExZDAzYTdlNzRkNmI0ZjFiZmRhODZlJlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZyZXNwb25zZS1jb250ZW50LXR5cGU9aW1hZ2UlMkZwbmcifQ.1PJ1Nm5RrL2I0wvGuhZT8NkpRlUhZb2OArvTwqIOCuQ)

![dashboard_snapo](https://private-user-images.githubusercontent.com/121956665/617088894-71a8953e-22c5-432e-a784-a1197d789bfb.png?jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3ODMxNjIyNzAsIm5iZiI6MTc4MzE2MTk3MCwicGF0aCI6Ii8xMjE5NTY2NjUvNjE3MDg4ODk0LTcxYTg5NTNlLTIyYzUtNDMyZS1hNzg0LWExMTk3ZDc4OWJmYi5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjYwNzA0JTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI2MDcwNFQxMDQ2MTBaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT01OWQzODM2NWQzNWExYjI2ZTNiMzZmY2E2YzUyNjNhMWJkMmUwYWFjNzM1ZjY4ZTUxNDE4ZWVjMDAxOWY5MzFkJlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZyZXNwb25zZS1jb250ZW50LXR5cGU9aW1hZ2UlMkZwbmcifQ.jtxF_3u_JI8ZT1Nn872S7I_btUR0TUOrV6SALnqGDmE)

![dashboard_snapo](https://private-user-images.githubusercontent.com/121956665/617089053-ad298806-3a38-4373-9c52-c7d04c7428f4.png?jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3ODMxNjIzOTYsIm5iZiI6MTc4MzE2MjA5NiwicGF0aCI6Ii8xMjE5NTY2NjUvNjE3MDg5MDUzLWFkMjk4ODA2LTNhMzgtNDM3My05YzUyLWM3ZDA0Yzc0MjhmNC5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjYwNzA0JTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI2MDcwNFQxMDQ4MTZaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT0zYTU1M2RiNjNlZmQ0NTMxMjNhMGIwZWNjNWQyZTgzZDM1MmJlNjdlZTBlODIwM2M2MTkwZDgyOTVkZWRiNmZmJlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZyZXNwb25zZS1jb250ZW50LXR5cGU9aW1hZ2UlMkZwbmcifQ.NBFpepYer4q4gs5y0DbxajqwQd61_4P666geBDuSodA)
 
# Insights

A report was created on Power BI Desktop & it was then published to Power BI Service.

Following inferences can be drawn from the dashboard;

### [1] Recent Anomaly in Sum of Net Sales


- Sum of Net Sales was unexpectedly high on Tuesday, November 28, 2023. It had a value of 376491, which is outside the expected range of 14528.07-35555.93.

        Sum of Net Sales for City Jaipur was unusually high, which may have lifted the Sum of Net Sales total.
        Sum of Net Sales for Pincode 302001 was unusually high, which may have lifted the Sum of Net Sales total.
        Sum of Net Sales for State Rajasthan was unusually high, which may have lifted the Sum of Net Sales total.
        Sum of Net Sales for Customer ID 40 was unusually high, which may have lifted the Sum of Net Sales total.
        Sum of Net Sales for Product Line Home Appliances was unusually high, which may have lifted the Sum of Net Sales total.

- Sum of Net Sales was unexpectedly high on Thursday, November 24, 2022. It had a value of 651189, which is outside the expected range of 35738.14-56043.86.

        Sum of Net Sales for Customer ID 48 was unusually high, which may   have lifted the Sum of Net Sales total.
        Sum of Net Sales for Product Line Electronics was unusually high, which may have lifted the Sum of Net Sales total.
           
### [2] Trends 
- Recent Trend in Sum of Net Sales

        "Sum of Net Sales" increased by "236,992" between "2 June 2021" and "7 June 2021". The increase was primarily driven by "Order ID 1366, Customer ID 1, Product ID P004", and the "Electronics" product line. 
        Nagpur, Maharashtra (Pincode 440001) contributed the highest growth in Net Sales, while Order ID 3242, Customer ID 17, and Delhi (Pincode 110001) recorded the largest declines. 
        Overall, strong sales from key customers, products, and regions led to the significant growth in Net Sales during this period.

 ### [3] KPI analysis
 - Number of Orders Analysis

        Overall Number of Orders is currently at 3,510. 1 segments have significantly lower Number of Orders than others, and 5 segments have significantly higher Number of Orders.
 
