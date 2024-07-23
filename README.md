fun insertNdidSummarySettlementReportRepository(
    yearMonth: String,
    nodeType: String,
    transactionType: String,
    nodeName: String,
    nodeDescription: String,
    totalTransactionRequest: BigDecimal,
    totalBillingTransaction: BigDecimal,
    totalStamp: Int,
    totalFee: Int,
    vatAmount: BigDecimal,
    whtAmount: BigDecimal,
    netAmount: BigDecimal,
    createDateTime: LocalDateTime,
    createBy: String,
    fileName: String
): Long? {
    val databaseSchema = databaseConfig.scbdidAdminSchema
    val tableName = "ndid_summary_settlement_report"
    try {
        val query = """
            INSERT INTO $databaseSchema.$tableName (
                year_month, node_type, transaction_type, node_name, node_description,
                total_transaction_request, total_billing_transaction, total_stamp, total_fee, vat_amount,
                wht_amount, net_amount, create_date_time, create_by, file_name
            ) VALUES (
                :yearMonth, :nodeType, :transactionType, :nodeName, :nodeDescription,
                :totalTransactionRequest, :totalBillingTransaction, :totalStamp, :totalFee, :vatAmount,
                :whtAmount, :netAmount, :createDateTime, :createBy, :fileName
            )
        """.trimIndent()
        val parameterSource = MapSqlParameterSource()
        parameterSource.addValue("yearMonth", yearMonth)
        parameterSource.addValue("nodeType", nodeType)
        parameterSource.addValue("transactionType", transactionType)
        parameterSource.addValue("nodeName", nodeName)
        parameterSource.addValue("nodeDescription", nodeDescription)
        parameterSource.addValue("totalTransactionRequest", totalTransactionRequest)
        parameterSource.addValue("totalBillingTransaction", totalBillingTransaction)
        parameterSource.addValue("totalStamp", totalStamp)
        parameterSource.addValue("totalFee", totalFee)
        parameterSource.addValue("vatAmount", vatAmount)
        parameterSource.addValue("whtAmount", whtAmount)
        parameterSource.addValue("netAmount", netAmount)
        parameterSource.addValue("createDateTime", createDateTime)
        parameterSource.addValue("createBy", createBy)
        parameterSource.addValue("fileName", fileName)
        
        val generatedKeyHolder = KeyHolder()
        jdbcTemplate.update(query, parameterSource, generatedKeyHolder, arrayOf("id"))

        return generatedKeyHolder.key?.toLong()
    } catch (e: Exception) {
        logService.error(ErrorLogMessage(tableName, e))
        throw DatabaseException(e.message)
    }
}