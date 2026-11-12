# Retail Batch Data Engineering Project — Sample Data

Pipeline:
Landing Zone -> GCS Raw -> BigQuery Staging -> Validation/Transform -> History -> Audit -> Views -> Archive

## Files

reference/
- customers.csv: 1,000 customer master records
- products.csv: 250 product master records

landing/day_01/
- Baseline batch; mostly valid.

landing/day_02/
- Contains duplicate rows, missing customer/product IDs, and invalid master references.

landing/day_03/
- Contains duplicates, NULLs, invalid timestamps, malformed numeric values,
  inconsistent statuses, invalid references, and late-arriving rows.

test_scenarios/
- orders_2026-10-02_DUPLICATE_FILE.csv
  Exact duplicate of the day-02 orders file for idempotency / duplicate-file testing.

- orders_2026-10-04_SCHEMA_CHANGE.csv
  Adds discount_code and currency columns for schema-evolution testing.

- payments_2026-10-04.csv is intentionally missing.
  Use this as a missing-file / dependency-failure scenario.

## Schemas

orders:
order_id, customer_id, product_id, quantity, unit_price, order_ts, order_status, source_system

payments:
payment_id, order_id, payment_ts, payment_method, payment_status, paid_amount

customers:
customer_id, customer_name, email, city, state, created_at, is_active

products:
product_id, product_name, category, unit_price, updated_at, is_active

## Suggested BigQuery datasets

- retail_staging
- retail_history
- retail_audit
- retail_views

## Suggested history-table design

Partition:
DATE(order_ts)

Cluster:
customer_id, order_status

## Suggested audit fields

batch_id, source_file, source_count, staging_count, valid_count,
rejected_count, history_count, start_time, end_time, status, error_message

## Embedded practice scenarios

1. Duplicate records
2. Missing customer/product IDs
3. Invalid master references
4. Invalid timestamps
5. Malformed numeric values
6. Inconsistent status formatting
7. Late-arriving orders
8. Duplicate input file
9. Schema change
10. Missing dependent file

Recommended order:
1. Build the happy path using day_01.
2. Process day_02 and add duplicate / FK / reject handling.
3. Process day_03 and handle malformed data + late arrivals.
4. Use test_scenarios for idempotency, schema change, and missing-file recovery.
