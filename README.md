package th.co.scb.ndid.admin.service

import org.springframework.beans.factory.annotation.Autowired
import org.springframework.stereotype.Service
import org.springframework.web.multipart.MultipartFile
import th.co.scb.ndid.admin.model.domain.request.NdidSummarySettlementReportFileRequest
import th.co.scb.ndid.admin.repository.NdidSummarySettlementReportFileRepository
import java.io.BufferedReader
import java.io.InputStreamReader
import java.io.IOException
import org.slf4j.Logger
import org.slf4j.LoggerFactory

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
                BufferedReader(InputStreamReader(inputStream)).use { reader ->
                    var line: String?
                    while (reader.readLine().also { line = it } != null) {
                        // Process each line
                        logger.info(line)
                    }
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