 fun processExportReconcileSettlementReportExcelFile(yearMonthParam: String){
        if (yearMonthParam.isEmpty() || yearMonthParam.isBlank()) {
            throw BadRequestException(1810, AdminResponseOptions.INVALID_INPUT_PARAMETER.message)
        }
        var reconcileSettlementReportDbList: List<ReconcileTransactionMapperModel>? = null
        try {
            reconcileSettlementReportDbList = reconcileSettlementReportRepository.findReconcileSettlement(yearMonthParam)

        } catch (ex: Exception) {
            throw AdminException(AdminResponseOptions.ERROR_GET_RECONCILE_SETTLEMENT_REPORT)
        }
        try {
//            start work book
            val workbook = XSSFWorkbook()
            val sheetPayTo = workbook.createSheet("payTo")
            val sheetBillTO = workbook.createSheet("billTo")
            val spitYearMonth = yearMonthParam.split("/")
            val year  = spitYearMonth[0]
            val month = spitYearMonth[1]
            val header = (year + "/" + (month.toInt() + 1) + "Payment ( Record as of NDID Billing Document ) " + year + "/" + month + " cycle")

//            first row of sheet payTo and billTo
            sheetPayTo.createRow(0).createCell(0).cellStyle(fontBold(workbook)).setCellValue(header)
            sheetBillTO.createRow(0).createCell(0).setCellValue(header)
            
            val outputStream = FileOutputStream("./folderTestExcel/testExcel.xlsx")
            workbook.write(outputStream)
            workbook.close()
        }
        catch (ex : Exception){
            throw AdminException(AdminResponseOptions.ERROR_CONVERT_TO_EXCEL)
        }
    }

        fun fontBold(workbook: Workbook): CellStyle {
            val style = workbook.createCellStyle()
            val font = workbook.createFont()
            font.fontName = "Courier New"
            font.bold = true
            style.setFont(font)
            style.alignment = HorizontalAlignment.CENTER
            style.verticalAlignment = VerticalAlignment.CENTER
            return style
        }
