Subject: Signing Off — Thank You for Everything

Hi Team,

As I prepare to log off for the last time this Friday, I’m filled with a mix of gratitude and nostalgia. My time at Verizon has been nothing short of incredible, and it’s all because of the amazing people I’ve had the chance to work with — you.

Thank you for the collaboration, the challenges that helped me grow, the laughs we shared, and the genuine support along the way. This experience has left a lasting impression on me, both professionally and personally.

Though I’m moving on to explore new adventures, I’d love to stay connected. Please feel free to drop me a note anytime at shubham4516@gmail.com, or let’s stay in touch on LinkedIn: https://www.linkedin.com/in/shubhom-ai

Wishing you all continued success and a journey full of impact and meaning.

Until next time,
Shubham
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
