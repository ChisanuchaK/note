import java.util.concurrent.CompletableFuture

@Async
@Transactional
fun insertNdidTransactionFilesGroup(ndidTransactionFilesRequestBody: NdidTransactionFilesRequest) {
    val futures = listOf(
        CompletableFuture.runAsync {
            ndidTransactionFilesRepository.insertNdidTransactionFile(
                LocalDateTime.now(), LocalDateTime.now(), ndidTransactionFilesRequestBody.yearMonth,
                ndidTransactionFilesRequestBody.rpNdidFileName.originalFilename!!, CommonConstant.RP_NDID,
                ndidTransactionFilesMapModelMap.binaryDataRpNdidFileName, ndidTransactionFilesRequestBody.createBy
            )
        },
        CompletableFuture.runAsync {
            ndidTransactionFilesRepository.insertNdidTransactionFile(
                LocalDateTime.now(), LocalDateTime.now(), ndidTransactionFilesRequestBody.yearMonth,
                ndidTransactionFilesRequestBody.rpIdpFileName.originalFilename!!, CommonConstant.RP_IDP,
                ndidTransactionFilesMapModelMap.binaryDataRpIdpFileName, ndidTransactionFilesRequestBody.createBy
            )
        },
        CompletableFuture.runAsync {
            ndidTransactionFilesRepository.insertNdidTransactionFile(
                LocalDateTime.now(), LocalDateTime.now(), ndidTransactionFilesRequestBody.yearMonth,
                ndidTransactionFilesRequestBody.rpASFileName.originalFilename!!, CommonConstant.RP_AS,
                ndidTransactionFilesMapModelMap.binaryDataRpASFileName, ndidTransactionFilesRequestBody.createBy
            )
        },
        CompletableFuture.runAsync {
            ndidTransactionFilesRepository.insertNdidTransactionFile(
                LocalDateTime.now(), LocalDateTime.now(), ndidTransactionFilesRequestBody.yearMonth,
                ndidTransactionFilesRequestBody.idpRpFileName.originalFilename!!, CommonConstant.IDP_RP,
                ndidTransactionFilesMapModelMap.binaryDataIdpRpFileName, ndidTransactionFilesRequestBody.createBy
            )
        },
        CompletableFuture.runAsync {
            ndidTransactionFilesRepository.insertNdidTransactionFile(
                LocalDateTime.now(), LocalDateTime.now(), ndidTransactionFilesRequestBody.yearMonth,
                ndidTransactionFilesRequestBody.asRpFileName.originalFilename!!, CommonConstant.AS_RP,
                ndidTransactionFilesMapModelMap.binaryDataAsRpFileName, ndidTransactionFilesRequestBody.createBy
            )
        }
    )

    CompletableFuture.allOf(*futures.toTypedArray()).join()
}