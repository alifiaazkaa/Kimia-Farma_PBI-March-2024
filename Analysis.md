# Kimia-Farma_PBI-March-2024

CREATE TABLE kimia_farma.tabel_analisis AS
SELECT
  final.transaction_id,
  final.date,
  final.branch_id,
  cabang.branch_name,
  cabang.kota,
  cabang.provinsi,
  cabang.rating as rating_cabang,
  final.customer_name,
  final.product_id,
  product.product_name,
  product.price AS actual_price,
  final.discount_percentage,
  CASE
    WHEN final.price <= 50000 THEN 0.1
    WHEN final.price > 50000 AND final.price <= 100000 THEN 0.15
    WHEN final.price > 100000 AND final.price <= 300000 THEN 0.2
    WHEN final.price > 300000 AND final.price <= 500000 THEN 0.25
    WHEN final.price > 500000 THEN 0.3
    ELSE 0
  END AS persentase_gross_laba,
  final.price - (final.discount_percentage*final.price) AS nett_sales,
  (
    SELECT
      SUM (product.price)-SUM(product.price*final.discount_percentage)
      FROM
        kimia_farma.kf_final_transaction AS final
      JOIN
        kimia_farma.kf_product AS product
        ON
          final.product_id = product.product_id
  ) AS nett_profit,
  final.rating AS rating_transaksi
FROM
  kimia_farma.kf_final_transaction AS final
LEFT JOIN
  kimia_farma.kf_kantor_cabang AS cabang 
  ON 
    final.branch_id = cabang.branch_id
LEFT JOIN
  kimia_farma.kf_product AS product
  ON
    final.product_id = product.product_id
;
