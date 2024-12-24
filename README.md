This content is the same that you can read in the pdf file(test_Novasphere.pdf).

Test NovaSphere

Daniele Ugo Coppola

To create a complete project starting from raw leads data to actionable insights in Power BI, you can follow these steps:

Step 1: Data Preparation and Consolidation
 1. Import CSV Files: Start by loading all relevant CSV files into SQL Server.
 2. Merge Data:
 • Use SQL to clean and transform the data.
 • Write JOIN queries to merge these datasets based on common keys 
Example:
WITH ranked_calls AS (
    SELECT
        nova_sphere_id,
        i.form_time,
...
        g.net_sale,
        g.cancel_sale,
        ROW_NUMBER() OVER (PARTITION BY nova_sphere_id ORDER BY t.call_time DESC) AS max_rank,
    FROM internal i 
        LEFT JOIN leads l using (nova_sphere_id) 
        LEFT JOIN tasks t using (nova_sphere_id) 
        LEFT JOIN sets s using (contact_id)
        LEFT JOIN issued g using (contact_id)
)

    SELECT 
        nova_sphere_id,
        form_time,
...
        MAX(CASE WHEN max_rank = 1 THEN call_result END) AS last_call_result,
        status as appointment_result,
        gross_sale,
        net_sale,
        cancel_sale
    FROM ranked_calls
    group by nova_sphere_id
;

 • Create calculated fields in SQL, such as set rates, appointment rates, and conversion rates.

SELECT
    round(sum(net_sale) / 10.0 / count(nova_sphere_id),2) as revenue_x_lead
FROM nova_sphere
where region is not null;
After I provide some insight:
a.	The revenue per lead if we charge 10% of the sales is equal to 12.74 $.
I used the net_sale and counted the leads, this means that for every lead we can expect an average net revenue of 12.74 $
b.	The set rate of leads that will have an appointment is 0.212.
This means that if we have 100 leads, we can expect that ~ 21 will set an appointment
c.		
step  	value  	ratio
n_leads   	1025   	1.00
n_leads_called	920   	0.90
n_appointment	217   	0.24
n_agree_to_sale	37   	0.17
n_sale	28   	0.76
          
We can see from this table:
that the company called 90% of the 1025 leads, 
succeeded in taking appointment to 24% of the leads called,
convinced 17% of the clients during appointment to buy the kitchen furniture,
and 76% proceeded with payment.

Step 2: Export to Power BI
1.	Export csv file: 
a.	Single data source file (“nova_sphere.csv”)

 • Import in Power BI
1.	Power bi file (“Nova_sphere_rep.pbix”)







Step 3: Build Metrics and Reports
 1. Key Metrics in Power BI:
 • Sum of Sales: Create a SUM measure for total sales:
• Count of Appointments: Use COUNTROWS to calculate the total number of appointments.
•  It consider the creation of Measure:
count_net_sale = CALCULATE(
   DISTINCTCOUNT(nova_sphere[net_sale]),
    nova_sphere[net_sale] <> 0
)


 2. Visualizations:
 • Sales Overview:
 • Bar chart: Sum of Sales by Region or Client.
 • Line chart: Count of Appointments by Day.
• Card visuals to display set rates and conversion rates.
 • Lead Insights:
 • Table view: Show leads segmented by Source, Landing Page, and Thank You Page.

 3. Slicers and Filters:
 • Add slicers for Source of Leads, Landing Page, and Thank You Page to allow users to filter the report dynamically.

Outcome:

The final Power BI report provides a comprehensive dashboard where stakeholders can:
 • Track total sales, appointments, and leads.
 • Analyze sales performance by region and client.
 • Measure key rates (e.g., set rate, conversion rate).
 • Understand the impact of lead sources and landing pages on sales outcomes.

This workflow combines SQL for robust data preparation and Power BI for powerful visualization, making it easy to derive insights and make data-driven decisions.
 ![image](https://github.com/user-attachments/assets/bf17fe88-661e-471e-92e9-b40d033910fe)


![image](https://github.com/user-attachments/assets/4c1f072e-39e9-4c1b-8843-7ac2243734ab)


