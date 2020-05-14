---
title: '& 例外狀況 Kusto 內嵌錯誤-Azure 資料總管'
description: 本文說明 Azure 資料總管中的 Kusto 錯誤和例外狀況。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 97798fa62d588769636966c7155dc5f398bd001a
ms.sourcegitcommit: fd3bf300811243fc6ae47a309e24027d50f67d7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83382313"
---
# <a name="kustoingest-errors-and-exceptions"></a>Kusto。內嵌錯誤和例外狀況
在用戶端上進行內嵌處理期間的任何錯誤，都是以 c # 例外狀況來表示。

## <a name="failures"></a>失敗

### <a name="kustodirectingestclient-exceptions"></a>KustoDirectIngestClient 例外狀況

嘗試從多個來源內嵌時，在內嵌過程中可能會發生錯誤。 如果其中一個來源的內嵌失敗，則會記錄它，而且用戶端會繼續內嵌剩餘的來源。 在進行內嵌的所有來源之後，會擲回 `IngestClientAggregateException` ，其中包含 `IList<IngestClientException> IngestionErrors` 成員。

`IngestClientException`及其衍生類別包含欄位 `IngestionSource` 和 `Error` 欄位。 這兩個欄位會一起建立一個對應，從無法內嵌的來源，到嘗試內嵌時發生的錯誤。 此資訊可在清單中用 `IngestionErrors` 來調查哪些來源的內嵌失敗和原因。 `IngestClientAggregateException`例外狀況也包含布林值屬性 `GlobalError` ，指出所有來源是否發生錯誤。

### <a name="failures-ingesting-from-files-or-blobs"></a>從檔案或 blob 內嵌的失敗

如果嘗試從 blob 或檔案內嵌時發生的內嵌失敗，即使旗標設定為，也不會刪除內嵌來源 `deleteSourceOnSuccess` `true` 。 系統會保留來源以供進一步分析。 瞭解錯誤的來源之後，如果錯誤不是來自內嵌來源本身，則用戶端的使用者可能會嘗試 reingest 它。

### <a name="failures-ingesting-from-idatareader"></a>從 IDataReader 內嵌的失敗

從 DataReader 內嵌時，要內嵌的資料會儲存到預設位置為的暫存資料夾中 `<Temp Path>\Ingestions_<current date and time>` 。 成功內嵌之後，一律會刪除此預設資料夾。

在 `IngestFromDataReader` 和 `IngestFromDataReaderAsync` 方法中，旗標 `retainCsvOnFailure` （其預設值為 `false` ）會判斷檔案是否應在失敗的內嵌之後保留。 如果此旗標設定為 `false` ，則不會保存失敗的內嵌資料，因此很難瞭解發生錯誤的原因。

## <a name="kustoqueuedingestclient-exceptions"></a>KustoQueuedIngestClient 例外狀況

`KustoQueuedIngestClient`藉由將訊息上傳至 Azure 佇列來內嵌資料。 如果在佇列進程之前或期間發生錯誤， `IngestClientAggregateException` 則會在進程結束時擲回。 擲回的例外狀況包含的集合 `IngestClientException` ，其中包含每個失敗的來源，而且尚未張貼至佇列。 嘗試張貼訊息時發生的錯誤也會擲回。

### <a name="posting-to-queue-failures-with-a-file-or-blob-as-a-source"></a>以檔案或 blob 做為來源張貼到佇列失敗

如果使用 `KustoQueuedIngestClient` 的方法發生錯誤 `IngestFromFile/IngestFromBlob` ，則不會刪除來源，即使 `deleteSourceOnSuccess` 旗標設定為也一樣 `true` 。 相反地，會保留來源以供進一步分析。 

瞭解錯誤的來源之後，如果錯誤不是來自內嵌來源本身，則用戶端的使用者可能會嘗試使用與失敗來源相關的方法來 requeue 資料 `IngestFromFile/IngestFromBlob` 。 

### <a name="posting-to-queue-failures-with-idatareader-as-a-source"></a>張貼到佇列失敗，並以 IDataReader 作為來源

使用 DataReader 來源時，張貼至佇列的資料會儲存到預設位置為的暫存資料夾中 `<Temp Path>\Ingestions_<current date and time>` 。 成功將資料張貼到佇列之後，一律會刪除此資料夾。
在 `IngestFromDataReader` 和 `IngestFromDataReaderAsync` 方法中，旗標 `retainCsvOnFailure` （其預設值為 `false` ）會判斷檔案是否應在失敗的內嵌之後保留。 如果此旗標設定為 `false` ，則不會保存失敗的內嵌資料，因此很難瞭解發生錯誤的原因。

### <a name="common-failures"></a>常見失敗
|錯誤                         |原因           |降低                                   |
|------------------------------|-----------------|---------------------------------------------|
|資料庫 <database name> 名稱不存在| 資料庫不存在|檢查資料庫的資料庫名稱 `kustoIngestionProperties` |
|找不到類型為「資料表」的實體「資料表名稱不存在」。|資料表不存在，而且沒有 CSV 對應。| 新增 CSV 對應/建立必要的資料表 |
|<blob path>排除的 Blob 原因： JSON 模式必須使用 jsonMapping 參數來內嵌| 未提供 JSON 對應時，會內嵌 JSON。|提供 JSON 對應 |
|無法下載 blob：「遠端伺服器傳回錯誤：（404）找不到。」| Blob 不存在。|請確認 blob 是否存在。 如果存在，請重試並聯絡 Kusto 小組 |
|JSON 資料行對應無效：兩個或多個對應元素指向相同的資料行。| JSON 對應有2個數據行具有不同的路徑|修正 JSON 對應 |
|EngineError-[UtilsException] `IngestionDownloader.Download` ：一個或多個檔案無法下載（請搜尋 KustoLogs 中的 ActivityID： <GUID1> ，RootActivityId： <GUID2> ）| 無法下載一或多個檔案。 |重試 |
|無法剖析：識別碼為 ' ' 的資料流程 <stream name> 具有格式不正確的 CSV 格式，每個 ValidationOptions 原則失敗 |CSV 檔案格式不正確（例如，每一行沒有相同數目的資料行）。 只有在驗證原則設定為時，才會失敗 `ValidationOptions` 。 ValidateCsvInputConstantColumns |檢查您的 CSV 檔案。 此訊息僅適用于 CSV/TSV 檔案 |
|`IngestClientAggregateException`出現錯誤訊息「遺漏有效的共用存取簽章的強制參數」 |所使用的 SAS 屬於服務，而不是儲存體帳戶 |使用儲存體帳戶的 SAS |

### <a name="ingestion-error-codes"></a>內嵌錯誤代碼

為了協助以程式設計方式處理內嵌失敗，會以數值錯誤碼（）擴充失敗資訊 `IngestionErrorCode enumeration` 。

|ErrorCode                                      |原因                                                        |
|-----------------------------------------------|--------------------------------------------------------------|
|Unknown                                        | 發生未知錯誤|
|Stream_LowMemoryCondition                      | 作業已用盡記憶體|
|Stream_WrongNumberOfFields                     | CSV 檔的欄位數目不一致|
|Stream_InputStreamTooLarge                     | 針對內嵌提交的檔已超過允許的大小|
|Stream_NoDataToIngest                          | 找不到可內嵌的資料流程|
|Stream_DynamicPropertyBagTooLarge              | 內嵌資料中的其中一個動態資料行包含太多唯一的屬性|
|Download_SourceNotFound                        | 無法從 Azure 儲存體下載來源-找不到來源|
|Download_AccessConditionNotSatisfied           | 無法從 Azure 儲存體下載來源-拒絕存取|
|Download_Forbidden                             | 無法從 Azure 儲存體下載來源-不允許存取|
|Download_AccountNotFound                       | 無法從 Azure 儲存體下載來源-找不到帳戶|
|Download_BadRequest                            | 無法從 Azure 儲存體下載來源-不正確的要求|
|Download_NotTransient                          | 無法從 Azure 儲存體下載來源-不是暫時性錯誤|
|Download_UnknownError                          | 無法從 Azure 儲存體下載來源-不明錯誤|
|UpdatePolicy_QuerySchemaDoesNotMatchTableSchema| 無法叫用更新原則。 查詢架構不符合資料表架構|
|UpdatePolicy_FailedDescendantTransaction       | 無法叫用更新原則。 失敗的下階交易式更新原則|
|UpdatePolicy_IngestionError                    | 無法叫用更新原則。 發生內嵌錯誤|
|UpdatePolicy_UnknownError                      | 無法叫用更新原則。 發生未知錯誤|
|BadRequest_MissingJsonMappingtFailure          | 未使用 jsonMapping 參數內嵌 JSON 模式|
|BadRequest_InvalidOrEmptyBlob                  | Blob 無效或為空的 zip 封存|
|BadRequest_DatabaseNotExist                    | 資料庫不存在|
|BadRequest_TableNotExist                       | 資料表不存在|
|BadRequest_InvalidKustoIdentityToken           | 不正確 Kusto 身分識別權杖|
|BadRequest_UriMissingSas                       | 不明 blob 儲存體中沒有 SAS 的 blob 路徑|
|BadRequest_FileTooLarge                        | 嘗試內嵌太大的檔案|
|BadRequest_NoValidResponseFromEngine           | 內嵌命令沒有有效的回復|
|BadRequest_TableAccessDenied                   | 拒絕存取資料表|
|BadRequest_MessageExhausted                    | 訊息已耗盡|
|General_BadRequest                             | 一般錯誤的要求。 可能會提示內嵌至不存在的資料庫/資料表|
|General_InternalServerError                    | 發生內部伺服器錯誤|

## <a name="detailed-exceptions-reference"></a>詳細的例外狀況參考

### <a name="cloudqueuesnotfoundexception"></a>CloudQueuesNotFoundException

當資料管理叢集未傳回任何佇列時引發

基類：[例外](https://msdn.microsoft.com/library/system.exception(v=vs.110).aspx)狀況

|欄位名稱 |類型     |意義
|-----------|---------|------------------------------|
|錯誤      | 字串  | 嘗試從 DM 取得佇列時發生的錯誤
                            
只有在使用[Kusto 佇列內嵌用戶端](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)時才相關。
在內嵌程式期間，會嘗試幾次來抓取連結至 DM 的 Azure 佇列。 當這些嘗試失敗時，包含失敗原因的例外狀況會在 [錯誤] 欄位中引發。 可能也會引發 ' InnerException ' 欄位中的內部例外狀況。


### <a name="cloudblobcontainersnotfoundexception"></a>CloudBlobContainersNotFoundException

未從資料管理叢集傳回任何 blob 容器時引發

基類：[例外](https://msdn.microsoft.com/library/system.exception(v=vs.110).aspx)狀況

|欄位名稱   |類型     |意義       
|-------------|---------|------------------------------|
|KustoEndpoint| 字串  | 相關 DM 的端點
                            
只有在使用[Kusto 佇列內嵌用戶端](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)時才相關。  
內嵌不在 Azure 容器中的來源（例如檔案、DataReader 或資料流程）時，會將資料上傳至暫存 blob 以進行內嵌。 當找不到可將資料上傳至的容器時，就會引發例外狀況。

### <a name="duplicateingestionpropertyexception"></a>DuplicateIngestionPropertyException

當內嵌屬性設定超過一次時引發

基類：[例外](https://msdn.microsoft.com/library/system.exception(v=vs.110).aspx)狀況

|欄位名稱   |類型     |意義       
|-------------|---------|------------------------------------|
|PropertyName | 字串  | 重複屬性的名稱。
                            
### <a name="postmessagetoqueuefailedexception"></a>PostMessageToQueueFailedException

將訊息張貼到佇列失敗時引發

基類：[例外](https://msdn.microsoft.com/library/system.exception(v=vs.110).aspx)狀況

|欄位名稱   |類型     |意義       
|-------------|---------|---------------------------------|
|QueueUri     | 字串  | 佇列的 URI
|錯誤        | 字串  | 嘗試張貼至佇列時所產生的錯誤訊息
                            
只有在使用[Kusto 佇列內嵌用戶端](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)時才相關。  
佇列的內嵌用戶端會藉由將訊息上傳至相關的 Azure 佇列來內嵌資料。 如果發生 post 失敗，則會引發例外狀況。 它會包含佇列 URI、[錯誤] 欄位中失敗的原因，以及 [InnerException] 欄位中可能發生的內部例外狀況。

### <a name="dataformatnotspecifiedexception"></a>DataFormatNotSpecifiedException

當需要資料格式，但未在中指定時引發`IngestionProperties`

基類： IngestClientException

從資料流程內嵌時，必須在[IngestionProperties](kusto-ingest-client-reference.md#class-kustoingestionproperties)中指定資料格式，才能適當地內嵌資料。 當未指定時，就會引發這個例外狀況 `IngestionProperties.Format` 。

### <a name="invaliduriingestclientexception"></a>InvalidUriIngestClientException

提交不正確 blob URI 做為內嵌來源時引發

基類： IngestClientException

### <a name="compressfileingestclientexception"></a>CompressFileIngestClientException

當內嵌用戶端無法壓縮提供用於內嵌的檔案時引發

基類： IngestClientException

檔案會先壓縮，再進行內嵌。 當嘗試壓縮檔案失敗時，就會引發例外狀況。

### <a name="uploadfiletotempblobingestclientexception"></a>UploadFileToTempBlobIngestClientException

當內嵌用戶端無法上傳提供來內嵌至暫存 blob 的來源時引發

基類： IngestClientException

### <a name="sizelimitexceededingestclientexception"></a>SizeLimitExceededIngestClientException

當內嵌來源太大時引發

基類： IngestClientException

|欄位名稱   |類型     |意義       
|-------------|---------|-----------------------|
|大小         | long    | 內嵌來源的大小
|MaxSize      | long    | 允許內嵌的最大大小

如果內嵌來源超過4GB 的最大大小，則會擲回例外狀況。 `IgnoreSizeLimit` [IngestionProperties 類別](kusto-ingest-client-reference.md#class-kustoingestionproperties)中的旗標可以覆寫大小驗證。 不過，不建議您內嵌[大於 1 GB 的單一來源](about-kusto-ingest.md#ingestion-best-practices)。

### <a name="uploadfiletotempblobingestclientexception"></a>UploadFileToTempBlobIngestClientException

當內嵌用戶端無法上傳提供來內嵌至暫存 blob 的檔案時引發

基類： IngestClientException

### <a name="directingestclientexception"></a>DirectIngestClientException

當執行直接內嵌時發生一般錯誤時引發

基類： IngestClientException

### <a name="queuedingestclientexception"></a>QueuedIngestClientException

在執行佇列內嵌時發生錯誤時引發

基類： IngestClientException

### <a name="ingestclientaggregateexception"></a>IngestClientAggregateException

在內嵌期間發生一或多個錯誤時引發

基類： [AggregateException](https://msdn.microsoft.com/library/system.aggregateexception(v=vs.110).aspx)

|欄位名稱      |類型                             |意義       
|----------------|---------------------------------|-----------------------|
|IngestionErrors | IList<IngestClientException>    | 嘗試內嵌時所發生的錯誤，以及與它們相關的來源
|IsGlobalError   | bool                            | 指出是否針對所有來源發生例外狀況

## <a name="next-steps"></a>後續步驟

如需機器碼中錯誤的詳細資訊，請參閱[機器碼中的錯誤](../../concepts/errorsinnativecode.md)。
