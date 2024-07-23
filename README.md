package th.co.scb.ndid.admin.repository


import org.springframework.beans.factory.annotation.Autowired
import org.springframework.jdbc.core.namedparam.MapSqlParameterSource
import org.springframework.jdbc.core.namedparam.NamedParameterJdbcTemplate
import org.springframework.stereotype.Repository
import th.co.scb.ndid.admin.config.DatabaseConfig

@Repository
class NdidSummarySettlementReportRepositoryImp (
    @Autowired private  val databaseConfig : DatabaseConfig

) : NdidSummarySettlementReportFileRepository{
    @Autowired
    private lateinit var jdbcTemplate: NamedParameterJdbcTemplate
    override fun deleteNdidSummarySettlementReportByYearMonthRepository(yearMonth: String) {
        val databaseSchema = databaseConfig.scbdidAdminSchema
        val tableName = "ndid_summary_settlement_report"
        val query = "  delete from $databaseSchema.$tableName where `year_month` = :yearMonth"
        val parameterSource = MapSqlParameterSource()
        parameterSource.addValue("yearMonth" , yearMonth)
        jdbcTemplate.update(query , parameterSource)
    }
}
