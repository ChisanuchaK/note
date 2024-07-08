package th.co.scb.ndid.admin.service

import com.fasterxml.jackson.databind.ObjectMapper
import org.springframework.beans.factory.annotation.Autowired
import org.springframework.stereotype.Service
import org.springframework.web.multipart.MultipartFile
import th.co.scb.ndid.admin.model.domain.request.NdidSummarySettlementReportFileRequest
import th.co.scb.ndid.admin.repository.NdidSummarySettlementReportFileRepository
import java.io.BufferedReader
import java.io.File
import java.io.FileReader
import java.util.Base64
import java.io.IOException
import org.slf4j.Logger
import org.slf4j.LoggerFactory

@Service
class NdidSummarySettlementReportFileService {

    @Autowired
    private lateinit var ndidSummarySettlementReportFileRepository : NdidSummarySettlementReportFileRepository

    private val logger: Logger = LoggerFactory.getLogger(NdidSummarySettlementReportFileService::class.java)

    fun convertMultipartFileToFile(file: MultipartFile): File {
        val convertFile = File(file.originalFilename!!)
        file.transferTo(convertFile)
        return convertFile
    }

    fun insertNdidSummarySettlementReportFileService(ndidSummarySettlementReportFileRequest: NdidSummarySettlementReportFileRequest) {
        if (ndidSummarySettlementReportFileRequest.file.isEmpty) {
            logger.error("File is empty")
            throw IllegalArgumentException("File is empty")
        }

        val file = convertMultipartFileToFile(ndidSummarySettlementReportFileRequest.file)

        try {
            BufferedReader(FileReader(file)).use { reader ->
                var line: String?
                while (reader.readLine().also { line = it } != null) {
                    // Process each line
                    logger.info(line)
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