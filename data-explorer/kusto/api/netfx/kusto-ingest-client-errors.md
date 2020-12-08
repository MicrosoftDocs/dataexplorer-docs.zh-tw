---
title: Kusto & 例外狀況的內嵌錯誤-Azure 資料總管
description: 本文說明 Kusto 在 Azure 資料總管中的內嵌錯誤和例外狀況。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: a4da1c35d34a1677cc0608fdf70dcbdb5a3c7c73
ms.sourcegitcommit: c6cb2b1071048daa872e2fe5a1ac7024762c180e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96774600"
---
# <a name="kustoingest-errors-and-exceptions"></a>Kusto 內嵌錯誤和例外狀況
用戶端上的內嵌處理期間發生的任何錯誤都會以 c # 例外狀況表示。

## <a name="failures"></a>失敗

### <a name="kustodirectingestclient-exceptions"></a>KustoDirectIngestClient 例外狀況

嘗試從多個來源內嵌時，可能會在內嵌進程期間發生錯誤。 如果其中一個來源的內嵌失敗，則會記錄下來，且用戶端會繼續內嵌其餘的來源。 在移過所有的內嵌來源之後， `IngestClientAggregateException` 會擲回，其中包含 `IList<IngestClientException> IngestionErrors` 成員。

`IngestClientException` 和其衍生類別包含欄位 `IngestionSource` 和 `Error` 欄位。 這兩個欄位一起建立一個對應，從失敗內嵌的來源，到嘗試內嵌時所發生的錯誤。 此資訊可用於清單中， `IngestionErrors` 以調查哪些來源失敗的內嵌和原因。 `IngestClientAggregateException`例外狀況也包含布林值屬性 `GlobalError` ，指出所有來源是否發生錯誤。

### <a name="failures-ingesting-from-files-or-blobs"></a>從檔案或 blob 擷取的失敗

如果嘗試從 blob 或檔案內嵌時發生內嵌失敗，即使旗標設定為，也不會刪除內嵌來源 `deleteSourceOnSuccess` `true` 。 系統會保留來源，以供進一步分析。 一旦瞭解錯誤的來源，而且如果錯誤不是源自內嵌來源本身，用戶端的使用者可能會嘗試 reingest 該錯誤。

### <a name="failures-ingesting-from-idatareader"></a>從 IDataReader 擷取的失敗

從 DataReader 擷取時，要內嵌的資料會儲存到預設位置為的暫存資料夾中 `<Temp Path>\Ingestions_<current date and time>` 。 此預設資料夾一律會在成功內嵌之後刪除。

在 `IngestFromDataReader` 和 `IngestFromDataReaderAsync` 方法中，旗標 `retainCsvOnFailure` （其預設值為 `false` ）會判斷檔案是否應該在內嵌失敗之後保留。 如果此旗標設定為 `false` ，將不會保存因為內嵌失敗的資料，因此難以瞭解發生錯誤的原因。

## <a name="kustoqueuedingestclient-exceptions"></a>KustoQueuedIngestClient 例外狀況

`KustoQueuedIngestClient` 將訊息上傳至 Azure 佇列，以內嵌資料。 如果在佇列處理常式之前或期間發生錯誤， `IngestClientAggregateException` 則會在進程結束時擲回。 擲回的例外狀況包含的集合 `IngestClientException` ，其中包含每個失敗的來源，而且尚未張貼至佇列。 嘗試張貼訊息時所發生的錯誤也會擲回。

### <a name="posting-to-queue-failures-with-a-file-or-blob-as-a-source"></a>以檔案或 blob 作為來源張貼至佇列失敗

如果在使用的方法時發生錯誤 `KustoQueuedIngestClient` `IngestFromFile/IngestFromBlob` ，則即使旗標設定為，也不會刪除來源 `deleteSourceOnSuccess` `true` 。 相反地，系統會保留來源，以供進一步分析。 

一旦瞭解錯誤的來源，而且如果錯誤不是源自于內嵌來源本身，用戶端的使用者就可以使用失敗來源的相關方法，嘗試 requeue 資料 `IngestFromFile/IngestFromBlob` 。 

### <a name="posting-to-queue-failures-with-idatareader-as-a-source"></a>以 IDataReader 作為來源張貼至佇列失敗

使用 DataReader 來源時，張貼至佇列的資料會儲存到預設位置為的暫存資料夾中 `<Temp Path>\Ingestions_<current date and time>` 。 成功將資料張貼至佇列之後，此資料夾一律會被刪除。
在 `IngestFromDataReader` 和 `IngestFromDataReaderAsync` 方法中，旗標 `retainCsvOnFailure` （其預設值為 `false` ）會判斷檔案是否應該在內嵌失敗之後保留。 如果此旗標設定為 `false` ，將不會保存因為內嵌失敗的資料，因此難以瞭解發生錯誤的原因。

### <a name="common-failures"></a>常見失敗
|錯誤                         |原因           |降低                                   |
|------------------------------|-----------------|---------------------------------------------|
|資料庫 <database name> 名稱不存在| 資料庫不存在|檢查資料庫的資料庫名稱。 `kustoIngestionProperties` |
|找不到類型 ' Table ' 的實體 ' 資料表名稱 '。|資料表不存在，而且沒有 CSV 對應。| 新增 CSV 對應/建立必要的資料表 |
|<blob path>排除的 Blob 原因： JSON 模式必須以 jsonMapping 參數內嵌| 未提供 JSON 對應時的 JSON 內嵌。|提供 JSON 對應 |
|無法下載 blob：「遠端伺服器傳回錯誤： (404) 找不到」。| Blob 不存在。|確認 blob 存在。 如果存在，請重試並聯絡 Kusto 小組 |
|JSON 資料行對應無效：兩個以上的對應元素指向相同的資料行。| JSON 對應有2個具有不同路徑的資料行|修正 JSON 對應 |
|EngineError-[UtilsException] `IngestionDownloader.Download` ：有一或多個檔案無法下載 (搜尋 KustoLogs 的 ActivityID： <GUID1> ，RootActivityId： <GUID2>) | 一或多個檔案下載失敗。 |重試 |
|無法剖析：識別碼為 ' ' 的串流 <stream name> 有格式不正確的 CSV 格式，每個 ValidationOptions 原則失敗 |格式不正確的 CSV 檔案 (例如，每一行) 都沒有相同數目的資料行。 只有當驗證原則設定為時，才會失敗 `ValidationOptions` 。 ValidateCsvInputConstantColumns |檢查您的 CSV 檔案。 此訊息只適用于 CSV/TSV 檔案 |
|`IngestClientAggregateException` 出現錯誤訊息「缺少有效共用存取簽章的強制參數」 |使用的 SAS 屬於服務，而非儲存體帳戶 |使用儲存體帳戶的 SAS |

### <a name="ingestion-error-codes"></a>內嵌錯誤代碼

為了協助以程式設計方式處理內嵌失敗，會使用數位錯誤碼 () 來擴充失敗資訊 `IngestionErrorCode enumeration` 。

如需內嵌錯誤碼的完整清單，請參閱 [Azure 資料總管中](../../../error-codes.md)的內嵌錯誤碼。

## <a name="detailed-exceptions-reference"></a>詳細的例外狀況參考

### <a name="cloudqueuesnotfoundexception"></a>CloudQueuesNotFoundException

當資料管理叢集未傳回任何佇列時引發

基類：[例外](/dotnet/api/system.exception)狀況

|欄位名稱 |類型     |意義
|-----------|---------|------------------------------|
|錯誤      | String  | 嘗試從 DM 取出佇列時發生的錯誤
                            
只有在使用 [Kusto 佇列內嵌用戶端](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)時才適用。
在內嵌程式中，會嘗試進行幾次，以取得連結至 DM 的 Azure 佇列。 當這些嘗試失敗時，會在 [錯誤] 欄位中引發包含失敗原因的例外狀況。 也可能會引發 ' InnerException ' 欄位中的內部例外狀況。


### <a name="cloudblobcontainersnotfoundexception"></a>CloudBlobContainersNotFoundException

當資料管理叢集中沒有傳回任何 blob 容器時引發

基類：[例外](/dotnet/api/system.exception)狀況

|欄位名稱   |類型     |意義       
|-------------|---------|------------------------------|
|KustoEndpoint| String  | 相關 DM 的端點
                            
只有在使用 [Kusto 佇列內嵌用戶端](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)時才適用。  
擷取尚未在 Azure 容器中的來源（例如檔案、DataReader 或串流）時，資料會上傳至暫存 blob 進行內嵌。 找不到要上傳資料的容器時，就會引發例外狀況。

### <a name="duplicateingestionpropertyexception"></a>DuplicateIngestionPropertyException

內嵌屬性設定超過一次時引發

基類：[例外](/dotnet/api/system.exception)狀況

|欄位名稱   |類型     |意義       
|-------------|---------|------------------------------------|
|PropertyName | String  | 重複屬性的名稱
                            
### <a name="postmessagetoqueuefailedexception"></a>PostMessageToQueueFailedException

將訊息張貼到佇列失敗時引發

基類：[例外](/dotnet/api/system.exception)狀況

|欄位名稱   |類型     |意義       
|-------------|---------|---------------------------------|
|QueueUri     | String  | 佇列的 URI
|錯誤        | String  | 嘗試張貼至佇列時所產生的錯誤訊息
                            
只有在使用 [Kusto 佇列內嵌用戶端](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)時才適用。  
排入佇列的內嵌用戶端會將訊息上傳至相關的 Azure 佇列，以內嵌資料。 如果有 post 失敗，則會引發例外狀況。 它會包含佇列 URI、[錯誤] 欄位中失敗的原因，而且可能是 [InnerException] 欄位中的內部例外狀況。

### <a name="dataformatnotspecifiedexception"></a>DataFormatNotSpecifiedException

當需要資料格式但未在中指定時引發 `IngestionProperties`

基類： IngestClientException

從資料流程擷取時，必須在 [IngestionProperties](kusto-ingest-client-reference.md#class-kustoingestionproperties)中指定資料格式，才能適當地內嵌資料。 當未指定時，就會引發這個例外狀況 `IngestionProperties.Format` 。

### <a name="invaliduriingestclientexception"></a>InvalidUriIngestClientException

當不正確 blob URI 提交為內嵌來源時引發

基類： IngestClientException

### <a name="compressfileingestclientexception"></a>CompressFileIngestClientException

內嵌用戶端無法壓縮提供進行內嵌的檔案時引發

基類： IngestClientException

先壓縮檔案，再進行內嵌。 嘗試壓縮檔案失敗時，會引發例外狀況。

### <a name="uploadfiletotempblobingestclientexception"></a>UploadFileToTempBlobIngestClientException

內嵌用戶端無法上傳提供給暫存 blob 的來源時引發

基類： IngestClientException

### <a name="sizelimitexceededingestclientexception"></a>SizeLimitExceededIngestClientException

內嵌來源太大時引發

基類： IngestClientException

|欄位名稱   |類型     |意義       
|-------------|---------|-----------------------|
|大小         | long    | 內嵌來源的大小
|MaxSize      | long    | 允許內嵌的最大大小

如果內嵌來源超過 4 GB 的最大值，則會擲回例外狀況。 `IgnoreSizeLimit` [IngestionProperties 類別](kusto-ingest-client-reference.md#class-kustoingestionproperties)中的旗標可以覆寫大小驗證。 但是，不建議內嵌 [大於 1 GB 的單一來源](about-kusto-ingest.md#ingestion-best-practices)。

### <a name="uploadfiletotempblobingestclientexception"></a>UploadFileToTempBlobIngestClientException

內嵌用戶端無法上傳提供給暫存 blob 的檔案時引發

基類： IngestClientException

### <a name="directingestclientexception"></a>DirectIngestClientException

在執行直接內嵌時發生一般錯誤時引發

基類： IngestClientException

### <a name="queuedingestclientexception"></a>QueuedIngestClientException

在執行排入佇列的內嵌發生錯誤時引發

基類： IngestClientException

### <a name="ingestclientaggregateexception"></a>IngestClientAggregateException

在內嵌期間發生一或多個錯誤時引發

基類： [AggregateException](/dotnet/api/system.aggregateexception)

|欄位名稱      |類型                             |意義       
|----------------|---------------------------------|-----------------------|
|IngestionErrors | IList<IngestClientException>    | 嘗試內嵌時所發生的錯誤，以及與其相關的來源
|IsGlobalError   | bool                            | 指出所有來源是否發生例外狀況


