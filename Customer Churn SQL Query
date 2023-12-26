USE [CIS5630-Twum-FinalProject];
GO

SELECT COUNT(customerID) AS "Total Customers"
FROM customer;
GO

SELECT ch.churn_label, COUNT(c.customerID) as counts 
FROM customer c
JOIN churn ch
ON c.customerID = ch.churnID
GROUP BY ch.churn_label;
GO


-- What is the tenure range for customers who churned?
SELECT 
	CASE
		WHEN tenure_months <= 12 THEN '1 year and below'
		WHEN tenure_months <= 24 THEN '2 years'
        WHEN tenure_months <= 36 THEN '3 years'
		ELSE 'Above 3 years'
	END AS Tenure,
	ROUND(COUNT(c.customerID)*100.0/SUM(COUNT(c.customerID)) OVER(),1) AS 'Churn %'
FROM customer C
JOIN churn ch
ON c.customerID = ch.churnID
WHERE ch.churn_label = 'Yes'
GROUP BY CASE
			WHEN tenure_months <= 12 THEN '1 year and below'
			WHEN tenure_months <= 24 THEN '2 years'
			WHEN tenure_months <= 36 THEN '3 years'
			ELSE 'Above 3 years'
		END
ORDER BY 'Churn %'DESC;
GO


-- What percentage of revenue does this Telco lose due to churned customers?
SELECT ch.churn_label,
		ROUND((SUM(b.total_charges)	*100.0)/SUM(SUM(b.total_charges)) OVER(), 2) AS 'Revenue %'
FROM churn ch
JOIN billing b
ON ch.churnID = b.billingID
GROUP BY ch.churn_label;
GO


-- What are the top 10 cities with the highest churn rates? 
SELECT TOP 10 c.city,
		COUNT(ch.churnID) AS 'Churned',
		CEILING(COUNT(CASE 
						WHEN ch.churn_label = 'Yes' THEN c.customerID ELSE NULL
					END)*100.0/COUNT(c.customerID)) AS 'churn %'
FROM customer c
JOIN churn ch
ON c.customerID = ch.churnID
GROUP BY c.city
HAVING COUNT(c.customerID) > 20
AND COUNT(CASE WHEN ch.churn_label = 'Yes' THEN c.customerID ELSE NULL END) > 0
ORDER BY 'churn %' DESC;
GO


-- What are the top reasons why customers leave?
SELECT 
    CASE
        WHEN churn_reason LIKE '%attitude%' or churn_reason LIKE '%support%' THEN 'Attitude of Support'
		WHEN churn_reason LIKE '%competitor%' THEN 'Competitor made better offer'
		WHEN churn_reason LIKE '%charges%' or churn_reason LIKE '%price%' THEN 'Higher Prices'
		WHEN churn_reason LIKE '%network%' or churn_reason LIKE '%dissatisfaction%' 
			   or churn_reason LIKE '%services%' 
			   or churn_reason LIKE '%speed%' THEN 'Product Dissatisfaction'
        		ELSE 'Others (eg. Dont know)'
    END AS Reason,
    COUNT(*) AS Count,
	COUNT(*) * 100 / SUM(COUNT(*)) OVER() AS Percentage
FROM churn
WHERE churn_label = 'Yes'
GROUP BY 
    CASE 
        WHEN churn_reason LIKE '%attitude%' or churn_reason LIKE '%support%' THEN 'Attitude of Support'
		WHEN churn_reason LIKE '%competitor%' THEN 'Competitor made better offer'
		WHEN churn_reason LIKE '%charges%' or churn_reason LIKE '%price%' THEN 'Higher Prices'
		WHEN churn_reason LIKE '%network%' or churn_reason LIKE '%dissatisfaction%' 
			or churn_reason LIKE '%services%' 
			or churn_reason LIKE '%speed%' THEN 'Product Dissatisfaction'
        		ELSE 'Others (eg. Dont know)'
    END
ORDER BY Count DESC;
GO


-- What Internet Type did churners have?
SELECT 
    internet_service, 
    COUNT(s.subscriptionID) AS 'Count',
    CEILING(COUNT(s.subscriptionID) * 100.0 / SUM(COUNT(s.subscriptionID)) OVER ()) AS 'Percentage'
FROM subscription s
JOIN churn ch ON s.subscriptionID = ch.churnID
WHERE churn_label = 'Yes'
GROUP BY internet_service;
GO


-- Did churners have premium tech support?
SELECT 
    tech_support, 
    COUNT(s.supportID) AS 'Count',
    CEILING(COUNT(s.supportID) * 100.0 / SUM(COUNT(s.supportID)) OVER ()) AS 'Percentage'
FROM support s
JOIN churn ch ON s.supportID = ch.churnID
WHERE churn_label = 'Yes'
GROUP BY tech_support
ORDER BY COUNT(s.supportID) DESC;
GO


-- What contract were churners on?

SELECT 
    contract, 
    COUNT(b.billingID) AS 'Count',
    CEILING(COUNT(b.billingID) * 100.0 / SUM(COUNT(b.billingID)) OVER ()) AS 'Percentage'
FROM billing b
JOIN churn ch ON b.billingID = ch.churnID
WHERE churn_label = 'Yes'
GROUP BY contract
ORDER BY COUNT(b.billingID) DESC;
GO
