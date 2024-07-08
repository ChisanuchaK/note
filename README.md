package th.co.scb.ndid.admin.service

import org.apache.poi.ss.usermodel.WorkbookFactory
import org.slf4j.Logger
import org.slf4j.LoggerFactory
import org.springframework.beans.factory.annotation.Autowired
import org.springframework.stereotype.Service
import org.springframework.web.multipart.MultipartFile
import th.co.scb.ndid.admin.model.domain.request.NdidSummarySettlementReportFileRequest
import th.co.scb.ndid.admin.repository.NdidSummarySettlementReportFileRepository
import java.io.IOException

@Service
class NdidSummarySettlementReportFileService {

    @Autowired
    private lateinit var ndidSummarySettlementReportFileRepository: NdidSummarySettlementReportFileRepository

    private val logger: Logger = LoggerFactory.getLogger(NdidSummarySettlementReportFileService::class.java)

    fun insertNdidSummarySettlementReportFileService(ndidSummarySettlementReportFileRequest: NdidSummarySettlementReportFileRequest) {
        if (ndidSummarySettlementReportFileRequest.file.isEmpty) {
            logger.error("File is empty")
            throw IllegalArgumentException("File is empty")
        }

        try {
            ndidSummarySettlementReportFileRequest.file.inputStream.use { inputStream ->
                val workbook = WorkbookFactory.create(inputStream)
                val sheet = workbook.getSheetAt(0)  // Assuming you want to read the first sheet

                for (row in sheet) {
                    val rowData = row.map { it.toString() }.joinToString(",")
                    // Process each row
                    logger.info(rowData)
                }
            }
        } catch (e: IOException) {
            logger.error("Error reading file", e)
            throw IOException("Error reading file", e)
        } catch (e: Exception) {
            logger.error("Unexpected error", e)
            throw e
        }
    }
}