/**NOTE:UPDATE THE FILE PATHS ACCORDINGLY **/



//Bucket creation:
gcloud storage buckets create gs://retail-batchdata-bucket

//copy data from landing zone to bucket
gcloud storage cp datasets/day0/landing/*.csv \
> gs://retail-batchdata-bucket/landing/2026-10-01/

//dataset creation
bq mk --dataset ecommerce01_staging

//bigquery load
bq load \
--source_format=CSV \
--skip_leading_rows=1 \
mahesh2726:ecommerce01_staging.payments_stg \
gs://retail-batchdata-bucket/landing/2026-10-01/payments_2026-10-01.csv \
> 'payment_id:STRING,order_id:STRING,payment_ts:STRING,payment_method:STRING,payment_status:STRING,paid_amount:STRING'
Waiting on bqjob_r5cba53397fed245f_000001a1d0d26799_1 ... (0s) Current status: DONE

// Count check 
wc -l datasets/day0/landing/*.csv