@Test
fun `when file is created then content is correct`() {
    val workbook = XSSFWorkbook()
    val workSheet = workbook.createSheet()
    val cellStyle = workbook.createCellStyle()
    cellStyle.fillForegroundColor = IndexedColors.RED.getIndex()
    cellStyle.fillPattern = FillPatternType.SOLID_FOREGROUND
    val firstCell = workSheet
        .createRow(0)
        .createCell(0)
    firstCell.setCellValue("SAVED VALUE")
    firstCell.cellStyle = cellStyle

    val tempFile = createTempFile("test_output_", ".xlsx")
    workbook.write(tempFile.outputStream())
    workbook.close()

    val inputWorkbook = WorkbookFactory.create(tempFile.toFile().inputStream())
    val firstSheet = inputWorkbook.getSheetAt(0)
    assertThat(firstSheet.getRow(0).getCell(0).stringCellValue).isEqualTo("SAVED VALUE")
}
