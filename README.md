# Fast Food Marketing Campaign A\B Test


## Goal of the Test 

Determine which of the three marketing campaigns (Promotions 1, 2, and 3) performs best in terms of mean weekly sales per LocationID and PromotionID.

<br>

# Data Aggregation 

The dataset was aggregated at the weekly level by LocationID, PromotionID, and week. Before statistical testing, it was further aggregated by LocationID and PromotionID to avoid inflating the sample size due to repeated measures over weeks.

# Calculations 
The table provides a pre-aggregated summary of three marketing promotions (P1, P2, P3), showing:

<br>

| promotion_id  | num_locations | mean_weekly_sales | standard_dev | 
| ----------- | ----------- | ----------- | ----------- | 
| 1 | 43 | 232.4 | 64.11 | 
| 2 | 47 | 189.32 | 57.99 | 
| 3 | 47 | 221.46 | 65.54 | 

<br>


    promotion_id: Identifies each campaign (1, 2, 3).

    num_locations: Count of stores testing each promotion 

    mean_avg_weekly_sales: Average sales per store during the campaign.

    standard_dev: Variability in sales across stores.

<br>

## Promotion 1 vs Promotion 2
<br>

For this comparison of three promotions, I'm using a Two-Sample T-test approach with pairwise comparisons between each promotion group and a confidence level of 99% to reduce false positives.
The calculator asks to insert count ( number of locations) , mean (average weekly sales per location) and standard deviation (Variability in sales across locations) .

<br>

<br>

<div>
  <img src="1%20vs%202.png" title="Promotion 1 vs 2"/>
</div>

<br>

	•	Mean Difference (d): 43.08

	•	Standard Error (SE): 12.93

	•	p-value: 0.00128

Promotion 1 performed significantly better than Promotion 2. With a p-value well below 0.01, we can **REJECT** the null hypothesis and say with 99% confidence that Promotion 1 **leads to higher average values** than Promotion 2.

<br>

## Promotion 1 vs Promotion 3
<br>

<br>

<div>
  <img src="1%20vs%203.png" title="Promotion 1 vs 3" />
</div>

<br>

	•	Mean Difference (d): 10.94

	•	Standard Error (SE): 13.67

	•	p-value: 0.43

There is **NO SIGNIFICANT** difference between Promotion 1 and Promotion 3 at the 99% confidence level. Even though Promotion 1 has a slightly higher mean (232.4 vs 221.46), the difference is not statistically meaningful.

<br>

## Promotion 2 vs Promotion 3
<br>

<br>

<div>
  <img src="2%20vs%203.png" title="Promotion 2 vs 3" />
</div>

<br>

	•	Mean Difference (d): -32.14

	•	Standard Error (SE): 12.77

	•	p-value: 0.0136

Although Promotion 3 outperformed Promotion 2, the difference is not statistically significant at the 99% level (only at the 95% level). This result is borderline, and with our stricter threshold due to multiple comparisons, we cannot confidently say Promotion 3 is better than Promotion 2.

<br>

## Conclusion 
<br>

| Comparison | Mean Difference | p-value | Conclusion                          |
|------------|------------------|---------|-------------------------------------|
| 1 vs 2     | +43.08           | 0.001   | ✅ Promotion 1 is significantly better |
| 1 vs 3     | +10.94           | 0.43    | ❌ No significant difference          |
| 2 vs 3     | -32.14           | 0.014   | ❌ Not significant at 99% level       |

<br>

# Decision

<br>

1. **Eliminate Promotion 2 immediately.**

2. **If our resources allow, run a focused P1 vs. P3 test with:**

   **Equal sample sizes.**

    **Longer duration (to reduce weekly variability).**

3. **If no retest is possible, between Promotion 1 and 3, either can be selected. No statistically significant difference exists, so business context (e.g., cost-effectiveness, customer engagement, scalability) should guide the final decision.**

<br>

# Appendix

### Query for table : 
```sql
WITH promotion_stats AS (
    SELECT
        location_id,
        promotion AS promotion_id,
        SUM(sales_in_thousands) AS total_sales,
        COUNT(DISTINCT week) AS weeks_count
    FROM
        tc-da-1.turing_data_analytics.wa_marketing_campaign
    GROUP BY
        location_id,
        promotion
)

SELECT
    promotion_id,
    COUNT(*) AS num_locations,
    ROUND(AVG(promotion_stats.total_sales), 2) AS mean_avg_weekly_sales,
    ROUND(stddev(promotion_stats.total_sales),2)  AS standard_dev
FROM
    promotion_stats
GROUP BY
    promotion_id
ORDER BY
    promotion_id;
```