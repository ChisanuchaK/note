import kotlinx.coroutines.async
import kotlinx.coroutines.awaitAll
import kotlinx.coroutines.coroutineScope

@Async
@Transactional
fun insertNdidTransactionFilesGroup(ndidTransactionFilesRequestBody: NdidTransactionFilesRequest) = runBlocking {
    coroutineScope {
        val tasks = listOf(
            async {
                ndidTransactionFilesRepository.insertNdidTransactionFile(
                    LocalDateTime.now(), LocalDateTime.now(), ndidTransactionFilesRequestBody.yearMonth,
                    ndidTransactionFilesRequestBody.rpNdidFileName.originalFilename!!, CommonConstant.RP_NDID,
                    ndidTransactionFilesMapModelMap.binaryDataRpNdidFileName, ndidTransactionFilesRequestBody.createBy
                )
            },
            async {
                ndidTransactionFilesRepository.insertNdidTransactionFile(
                    LocalDateTime.now(), LocalDateTime.now(), ndidTransactionFilesRequestBody.yearMonth,
                    ndidTransactionFilesRequestBody.rpIdpFileName.originalFilename!!, CommonConstant.RP_IDP,
                    ndidTransactionFilesMapModelMap.binaryDataRpIdpFileName, ndidTransactionFilesRequestBody.createBy
                )
            },
            async {
                ndidTransactionFilesRepository.insertNdidTransactionFile(
                    LocalDateTime.now(), LocalDateTime.now(), ndidTransactionFilesRequestBody.yearMonth,
                    ndidTransactionFilesRequestBody.rpASFileName.originalFilename!!, CommonConstant.RP_AS,
                    ndidTransactionFilesMapModelMap.binaryDataRpASFileName, ndidTransactionFilesRequestBody.createBy
                )
            },
            async {
                ndidTransactionFilesRepository.insertNdidTransactionFile(
                    LocalDateTime.now(), LocalDateTime.now(), ndidTransactionFilesRequestBody.yearMonth,
                    ndidTransactionFilesRequestBody.idpRpFileName.originalFilename!!, CommonConstant.IDP_RP,
                    ndidTransactionFilesMapModelMap.binaryDataIdpRpFileName, ndidTransactionFilesRequestBody.createBy
                )
            },
            async {
                ndidTransactionFilesRepository.insertNdidTransactionFile(
                    LocalDateTime.now(), LocalDateTime.now(), ndidTransactionFilesRequestBody.yearMonth,
                    ndidTransactionFilesRequestBody.asRpFileName.originalFilename!!, CommonConstant.AS_RP,
                    ndidTransactionFilesMapModelMap.binaryDataAsRpFileName, ndidTransactionFilesRequestBody.createBy
                )
            }
        )
        tasks.awaitAll()
    }
}