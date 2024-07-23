import org.springframework.jdbc.core.namedparam.MapSqlParameterSource
import org.springframework.jdbc.core.namedparam.NamedParameterJdbcTemplate
import org.springframework.jdbc.support.GeneratedKeyHolder
import org.springframework.jdbc.support.KeyHolder
import java.math.BigDecimal
import java.time.LocalDateTime

data class NdidSummarySettlementReport(
    val yearMonth: String,
    val nodeType: String,
    val transactionType: String,
    val nodeName: String,
    val nodeDescription: String,
    val totalTransactionRequest: BigDecimal,
    val totalBillingTransaction: BigDecimal,
    val totalStamp: Int,
    val totalFee: Int,
    val vatAmount: BigDecimal,
    val whtAmount: BigDecimal,
    val netAmount: BigDecimal,
    val createDateTime: LocalDateTime,
    val createBy: String,
    val fileName: String
)

fun insertNdidSummarySettlementReportList(
    reports: List<NdidSummarySettlementReport>
): List<Long> {
    val databaseSchema = databaseConfig.scbdidAdminSchema
    val tableName = "ndid_summary_settlement_report"
    val generatedKeys = mutableListOf<Long>()
    
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

        for (report in reports) {
            val parameterSource = MapSqlParameterSource()
            parameterSource.addValue("yearMonth", report.yearMonth)
            parameterSource.addValue("nodeType", report.nodeType)
            parameterSource.addValue("transactionType", report.transactionType)
            parameterSource.addValue("nodeName", report.nodeName)
            parameterSource.addValue("nodeDescription", report.nodeDescription)
            parameterSource.addValue("totalTransactionRequest", report.totalTransactionRequest)
            parameterSource.addValue("totalBillingTransaction", report.totalBillingTransaction)
            parameterSource.addValue("totalStamp", report.totalStamp)
            parameterSource.addValue("totalFee", report.totalFee)
            parameterSource.addValue("vatAmount", report.vatAmount)
            parameterSource.addValue("whtAmount", report.whtAmount)
            parameterSource.addValue("netAmount", report.netAmount)
            parameterSource.addValue("createDateTime", report.createDateTime)
            parameterSource.addValue("createBy", report.createBy)
            parameterSource.addValue("fileName", report.fileName)
            
            val keyHolder: KeyHolder = GeneratedKeyHolder()
            jdbcTemplate.update(query, parameterSource, keyHolder, arrayOf("id"))
            
            keyHolder.key?.toLong()?.let { generatedKeys.add(it) }
        }
    } catch (e: Exception) {
        logService.error(ErrorLogMessage(tableName, e))
        throw DatabaseException(e.message)
    }
    
    return generatedKeys
}