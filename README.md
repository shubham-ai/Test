CREATE OR REPLACE TABLE `vz-it-pr-fjpv-vzdsdo-0.ds_work_tbls_no_gsam.bm_dly_negative_sentiment_v2` AS (
  SELECT
    s.ecpd_profile_id,
    SUM(s.negative) AS negative
  FROM (
    SELECT
      rpt_dt,
      ecpd_profile_id,
      cust_id,
      cust_line_seq_id,
      COALESCE(SUM(CASE
        WHEN sentiment = 'negative' THEN 1
        ELSE 0
      END), 0) AS negative
    FROM (
      SELECT *
      FROM `ntl-prd-allvm.cust_line_call_intent_score_vbg_v`
      QUALIFY ROW_NUMBER() OVER (
        PARTITION BY cust_id, cust_line_seq_id
        ORDER BY rpt_dt DESC
      ) = 1
    )
    GROUP BY rpt_dt, ecpd_profile_id, cust_id, cust_line_seq_id
    HAVING negative > 0
  ) AS s
  GROUP BY s.ecpd_profile_id
);
