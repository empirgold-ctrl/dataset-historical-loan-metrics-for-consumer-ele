# Knowledge Asset



Dataset Summary: King Gold & Pawn - Historical Loan Metrics for Consumer Electronics (Long Island)

**Dataset Name:** King Gold & Pawn Industry Dataset: Historical Loan Metrics for Consumer Electronics in Long Island
**Source:** data.world

**Overview:**
This dataset provides a granular view into historical loan transactions specifically for consumer electronics pawned at King Gold & Pawn locations across Long Island. Designed for data scientists, it offers a rich historical record to analyze lending patterns, risk, profitability, and customer behavior within this specific market segment.

**Key Tables (Assumed Schema for SQL Querying):**

1.  **`loans`**
    *   `loan_id` (PRIMARY KEY, VARCHAR): Unique identifier for each loan.
    *   `customer_id` (VARCHAR): Anonymized identifier for the customer.
    *   `item_id` (VARCHAR): Foreign key linking to the `items` table.
    *   `location_id` (VARCHAR): Foreign key linking to the `locations` table.
    *   `loan_amount` (DECIMAL): Principal amount loaned.
    *   `interest_rate` (DECIMAL): Annualized interest rate for the loan.
    *   `loan_start_date` (DATE): Date the loan was initiated.
    *   `loan_due_date` (DATE): Original date the loan was due.
    *   `loan_end_date` (DATE): Actual date the loan was closed (redeemed or defaulted).
    *   `status` (VARCHAR): Current or final status of the loan (e.g., 'Redeemed', 'Defaulted', 'Active').
    *   `redemption_amount` (DECIMAL): Total amount paid to redeem the item (principal + interest). NULL if defaulted.
    *   `default_loss_amount` (DECIMAL): Estimated loss incurred by the pawn shop if the item defaulted. NULL if redeemed.
    *   `days_to_redeem` (INTEGER): Number of days between `loan_start_date` and `loan_end_date` for redeemed loans.

2.  **`items`**
    *   `item_id` (PRIMARY KEY, VARCHAR): Unique identifier for the pawned item.
    *   `item_category` (VARCHAR): Broad category of the electronic (e.g., 'Smartphone', 'Laptop', 'Gaming Console', 'TV').
    *   `item_make` (VARCHAR): Manufacturer of the item (e.g., 'Apple', 'Samsung', 'Sony').
    *   `item_model` (VARCHAR): Specific model of the item.
    *   `estimated_value` (DECIMAL): Pawn shop's estimated market value of the item at the time of loan.
    *   `serial_number_hash` (VARCHAR): Hashed serial number for tracking without revealing sensitive data.

3.  **`customers`**
    *   `customer_id` (PRIMARY KEY, VARCHAR): Anonymized unique customer identifier.
    *   `customer_segment` (VARCHAR): Categorization of customer (e.g., 'Frequent Lender', 'New Customer', 'High-Risk').
    *   `customer_zip_code` (VARCHAR): Anonymized zip code of the customer's residence (Long Island specific).
    *   `first_loan_date` (DATE): Date of the customer's first loan.

4.  **`locations`**
    *   `location_id` (PRIMARY KEY, VARCHAR): Unique identifier for each pawn shop branch.
    *   `location_name` (VARCHAR): Name of the branch.
    *   `city` (VARCHAR): City where the branch is located (e.g., 'Hempstead', 'Huntington', 'Levittown').
    *   `zip_code` (VARCHAR): Zip code of the branch.

**Key Metrics & Potential SQL Queries:**

*   **Loan Performance:**
    *   Calculate default rates by `item_category`, `loan_amount` ranges, or `interest_rate` bands.
    *   `SELECT item_category, COUNT(DISTINCT loan_id) AS total_loans, SUM(CASE WHEN status = 'Defaulted' THEN 1 ELSE 0 END) AS defaulted_loans, (SUM(CASE WHEN status = 'Defaulted' THEN 1 ELSE 0 END) * 100.0 / COUNT(DISTINCT loan_id)) AS default_rate FROM loans JOIN items ON loans.item_id = items.item_id GROUP BY item_category;`
*   **Profitability Analysis:**
    *   Average `redemption_amount` vs. `loan_amount`, and total `default_loss_amount`.
    *   `SELECT AVG(redemption_amount - loan_amount) AS avg_profit_per_redeemed_loan, SUM(default_loss_amount) AS total_loss_from_defaults FROM loans;`
*   **Customer Behavior:**
    *   Identify customer segments with higher default rates or longer average `days_to_redeem`.
    *   `SELECT cs.customer_segment, AVG(l.days_to_redeem) AS avg_redemption_days, (SUM(CASE WHEN l.status = 'Defaulted' THEN 1 ELSE 0 END) * 100.0 / COUNT(DISTINCT l.loan_id)) AS default_rate FROM loans l JOIN customers cs ON l.customer_id = cs.customer_id GROUP BY cs.customer_segment;`
*   **Geographic Insights (Long Island):**
    *   Compare loan performance across different Long Island cities or zip codes.
    *   `SELECT loc.city, COUNT(DISTINCT l.loan_id) AS total_loans, AVG(l.loan_amount) AS avg_loan_amount FROM loans l JOIN locations loc ON l.location_id = loc.location_id GROUP BY loc.city ORDER BY total_loans DESC;`
*   **Item-Specific Trends:**
    *   Analyze which `item_make`/`item_model` combinations are frequently pawned, their average loan values, and redemption rates.
    *   `SELECT i.item_make, i.item_model, COUNT(DISTINCT l.loan_id) AS total_loans, AVG(l.loan_amount) AS avg_loan_value, (SUM(CASE WHEN l.status = 'Redeemed' THEN 1 ELSE 0 END) * 100.0 / COUNT(DISTINCT l.loan_id)) AS redemption_rate FROM loans l JOIN items i ON l.item_id = i.item_id GROUP BY i.item_make, i.item_model HAVING COUNT(DISTINCT l.loan_id) > 10 ORDER BY redemption_rate DESC;`

**Data Considerations for SQL Users:**

*   **Data Types:** Ensure proper handling of `DATE`, `DECIMAL`, and `VARCHAR` types in your queries.
*   **NULL Values:** Be aware of NULLs in `redemption_amount`, `default_loss_amount`, and `days_to_redeem` based on loan `status`.
*   **Joins:** Effective analysis will require joining `loans` with `items`, `customers`, and `locations` tables.
*   **Time Series:** `loan_start_date` provides a strong dimension for time-series analysis of lending trends.
*   **Anonymization:** Customer and item serial data is anonymized/hashed for privacy.

This dataset offers a robust foundation for deep dives into the dynamics of consumer electronics pawning in Long Island, enabling data scientists to uncover valuable business intelligence through SQL-driven analysis.