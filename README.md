class GetReconcileSettlementReportMapper : RowMapper<GetReconcileSettlementReportModel> {
    override fun mapRow(rs: ResultSet, rowNum: Int): GetReconcileSettlementReportModel {
        return GetReconcileSettlementReportModel(
            rs.getString("year_month"),
            rs.Enum("transaction_type"),
            rs.getString("node_name_en"),
            rs.getString("node_name_th"),
            rs.getInt("billing_transaction_from_scb"),
            rs.getInt("billing_transaction_from_ndid"),
            rs.getInt("diff"),
            rs.getBigDecimal("total_fee")?:BigDecimal(0),
            rs.getBigDecimal("amount")?:BigDecimal(0),
            rs.getBigDecimal("vat")?:BigDecimal(0),
            rs.getBigDecimal("grand_total")?:BigDecimal(0),
            rs.getBigDecimal("account_balance")?:BigDecimal(0),
        )
    }
}
