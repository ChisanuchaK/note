import org.apache.poi.ss.usermodel.HorizontalAlignment
import org.apache.poi.ss.usermodel.VerticalAlignment
import org.apache.poi.xssf.usermodel.XSSFCellStyle
import org.apache.poi.xssf.usermodel.XSSFFont
import org.apache.poi.xssf.usermodel.XSSFWorkbook
import java.io.FileOutputStream

fun processExportReconcileSettlementReportExcelFile(yearMonthParam: String) {
    if (yearMonthParam.isEmpty() || yearMonthParam.isBlank()) {
        throw BadRequestException(1810, AdminResponseOptions.INVALID_INPUT_PARAMETER.message)
    }
    
    val reconcileSettlementReportDbList: List<ReconcileTransactionMapperModel>
    try {
        reconcileSettlementReportDbList = reconcileSettlementReportRepository.findReconcileSettlement(yearMonthParam)
    } catch (ex: Exception) {
        throw AdminException(AdminResponseOptions.ERROR_GET_RECONCILE_SETTLEMENT_REPORT)
    }

    if (reconcileSettlementReportDbList.isNullOrEmpty()) {
        throw AdminException(AdminResponseOptions.NO_DATA_FOUND.message)
    }

    try {
        val workbook = XSSFWorkbook()
        val sheetPayTo = workbook.createSheet("payTo")
        val sheetBillTo = workbook.createSheet("billTo")
        
        val spitYearMonth = yearMonthParam.split("/")
        if (spitYearMonth.size != 2) {
            throw BadRequestException(1810, "Invalid yearMonthParam format")
        }
        
        val year = spitYearMonth[0]
        val month = spitYearMonth[1].toIntOrNull() ?: throw BadRequestException(1810, "Invalid month value")
        
        val header = "${year}/${month + 1} Payment (Record as of NDID Billing Document) ${year}/${month} cycle"

        // First row of sheet payTo and billTo
        val payToHeaderCell = sheetPayTo.createRow(0).createCell(0)
        payToHeaderCell.cellStyle = fontBold(workbook)
        payToHeaderCell.setCellValue(header)
        
        val billToHeaderCell = sheetBillTo.createRow(0).createCell(0)
        billToHeaderCell.cellStyle = fontBold(workbook)
        billToHeaderCell.setCellValue(header)

        val outputStream = FileOutputStream("./folderTestExcel/testExcel.xlsx")
        workbook.write(outputStream)
        workbook.close()
        outputStream.close()
    } catch (ex: Exception) {
        throw AdminException(AdminResponseOptions.ERROR_CONVERT_TO_EXCEL)
    }
}

fun fontBold(workbook: XSSFWorkbook): XSSFCellStyle {
    val style = workbook.createCellStyle() as XSSFCellStyle
    val font = workbook.createFont() as XSSFFont
    font.fontName = "Courier New"
    font.bold = true
    style.setFont(font)
    style.alignment = HorizontalAlignment.CENTER
    style.verticalAlignment = VerticalAlignment.CENTER
    return style
}