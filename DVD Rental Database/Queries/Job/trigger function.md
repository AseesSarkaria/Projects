```sql
CREATE OR REPLACE FUNCTION update_summary_on_insert()
RETURNS TRIGGER AS $$
BEGIN
    -- Insert or update the summary table
    INSERT INTO summary (
        store_id,
        year,
        month,
        purchase_volume,
        revenue,
        mom_purchase_volume,
        mom_revenue,
        mom_purchases_to_revenue_ratio,
        biggest_purchase,
        smallest_purchase,
        max_min_difference,
        average_purchase,
        median_purchase,
        avg_med_difference,
        standard_deviation
    )
    SELECT 
        NEW.store_id,
        date_part('year', NEW.rental_date) AS year,
        date_part('month', NEW.rental_date) AS month,
        COUNT(*) AS purchase_volume,
        SUM(NEW.payment_id) AS revenue, -- Replace with actual revenue field
        NULL AS mom_purchase_volume, -- Placeholder for Month-over-Month calculations
        NULL AS mom_revenue,         -- Placeholder for Month-over-Month calculations
        NULL AS mom_purchases_to_revenue_ratio, -- Placeholder for Month-over-Month calculations
        MAX(NEW.payment_id) AS biggest_purchase, -- Replace with actual amount field
        MIN(
            CASE
                WHEN (NEW.payment_id > 0) THEN NEW.payment_id
                ELSE NULL
            END) AS smallest_purchase,
        MAX(NEW.payment_id) - MIN(
            CASE
                WHEN (NEW.payment_id > 0) THEN NEW.payment_id
                ELSE NULL
            END) AS max_min_difference,
        ROUND(AVG(NEW.payment_id), 2) AS average_purchase,
        PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY NEW.payment_id) AS median_purchase,
        ROUND(AVG(NEW.payment_id), 2) - PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY NEW.payment_id) AS avg_med_difference,
        ROUND(STDDEV(NEW.payment_id), 2) AS standard_deviation
    FROM detailed
    WHERE store_id = NEW.store_id
      AND date_part('year', rental_date) = date_part('year', NEW.rental_date)
      AND date_part('month', rental_date) = date_part('month', NEW.rental_date)
    GROUP BY store_id, date_part('year', NEW.rental_date), date_part('month', NEW.rental_date)
    ON CONFLICT (store_id, year, month)
    DO UPDATE SET
        purchase_volume = EXCLUDED.purchase_volume,
        revenue = EXCLUDED.revenue,
        mom_purchase_volume = NULL, -- Placeholder for recalculating Month-over-Month
        mom_revenue = NULL,         -- Placeholder for recalculating Month-over-Month
        mom_purchases_to_revenue_ratio = NULL, -- Placeholder for recalculating Month-over-Month
        biggest_purchase = EXCLUDED.biggest_purchase,
        smallest_purchase = EXCLUDED.smallest_purchase,
        max_min_difference = EXCLUDED.max_min_difference,
        average_purchase = EXCLUDED.average_purchase,
        median_purchase = EXCLUDED.median_purchase,
        avg_med_difference = EXCLUDED.avg_med_difference,
        standard_deviation = EXCLUDED.standard_deviation;

    RETURN NEW;
END;
$$ LANGUAGE plpgsql;