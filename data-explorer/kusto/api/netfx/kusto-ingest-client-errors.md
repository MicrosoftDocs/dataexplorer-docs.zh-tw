---
title: Kusto.ingest 參考 - 錯誤和異常 - Azure 資料資源管理器 |微軟文件
description: 本文介紹 Kusto.Ingest 引用 - Azure 資料資源管理器中的錯誤和異常。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: f8f50322a79dea8890b4a4ad5eaa78f0b8fe3bc0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81524339"
---
# <a name="kustoingest-reference---errors-and-exceptions"></a>庫斯特.Ingest 參考 - 錯誤和例外
用戶端的引入處理過程中的任何錯誤都通過 C# 異常向使用者代碼公開。

## <a name="failures-overview"></a>容錯概述

### <a name="kustodirectingestclient-exceptions"></a>庫斯特定向客戶端異常
在嘗試從多個源引入時,在引入其中一些源時可能會出現錯誤,而其他源可能已成功引入。 如果特定源的引入失敗,則記錄該引入,並且客戶端繼續引入用於引入的剩餘源。 在遍過所有來源進行攝入后,將引發`IngestClientAggregateException`一個包含成員`IList<IngestClientException> IngestionErrors`。
`IngestClientException`及其派生類包含一個字段`IngestionSource`和`Error`一 個字段,這些欄位共同形成從源的映射,該映射無法引入到嘗試引入時發生的錯誤。 可以使用「引入錯誤」清單中的資訊來調查哪些源未被引入以及原因。 `IngestClientAggregateException`例外還包含一個布爾屬性,`GlobalError`該屬性指示是否對所有源發生錯誤。

### <a name="failures-ingesting-from-files-or-blobs"></a>從檔案或 Blob 引入失敗 
如果嘗試從 blob_file 引入時發生引入失敗,`deleteSourceOnSuccess`則即使標誌`true`設置為 也不會刪除引入源。
保留源以進行進一步分析。 一旦瞭解了錯誤的來源,並且由於錯誤並非來自引入源本身,用戶端的使用者可能會嘗試重新引入它。

### <a name="failures-ingesting-from-idatareader"></a>從 IDataReader 引入失敗
從 DataReader 引入時,要引入的數據將保存到預設`<Temp Path>\Ingestions_<current date and time>`位置為的臨時資料夾。 此文件始終在成功引入後刪除。<BR>
在`IngestFromDataReader`和`IngestFromDataReaderAsync`方法中`retainCsvOnFailure`,預設值`false`為的標記確定是否應在引入失敗後保留檔。 如果此標誌設置為`false`,則無法引入的數據將不會持久化,因此很難理解問題所在。

## <a name="kustoqueuedingestclient-exceptions"></a>庫斯特托排隊處理客戶端異常
KustoQueuedestClient 通過將消息上傳到 Azure 佇列來引入數據。 如果在排隊過程之前和期間發生任何錯誤,則在執行結束時`IngestClientAggregateException`將引發一個`IngestClientException`錯誤 ,其中包含未發佈到佇列的源(對於每個失敗)以及嘗試發佈消息時發生的錯誤。

### <a name="posting-to-queue-failures-with-file-or-blob-as-a-source"></a>將檔案或 Blob 為源過帳到佇列失敗
如果使用 KustoQueuedingestClient 的引入源檔/Ingest FromBlob 方法時發生錯誤,則源不會`deleteSourceOnSuccess`被刪除,`true`即使 標誌設置為 ,而是保留以進行進一步分析。 一旦瞭解了錯誤的來源,並且由於錯誤並非來自源本身,用戶端的使用者可能會嘗試使用與失敗源相關的 Ingest FromFile/Ingest FromBlob 方法重新排隊數據。 

### <a name="posting-to-queue-failures-with-idatareader-as-a-source"></a>使用 IDataReader 為源過帳到佇列失敗
使用 DataReader 源時,要發布到佇列的數據將保存到預設`<Temp Path>\Ingestions_<current date and time>`位置為的臨時資料夾。
在將數據成功發佈到佇列后,始終會刪除此資料夾。
在`IngestFromDataReader`和`IngestFromDataReaderAsync`方法中`retainCsvOnFailure`,預設值`false`為的標記確定是否應在引入失敗後保留檔。 如果此標誌設置為`false`,則無法引入的數據將不會持久化,因此很難理解問題所在。

### <a name="common-failures"></a>常見故障
|錯誤|原因|降低|
|------------------------------|----|------------|
|資料庫<database name>名稱不存在| 資料庫不存在|在 kustoInges 屬性/建立資料庫時檢查資料庫名稱 |
|找不到實體「不存在的表名稱」 類型的「表」。|該表不存在,並且沒有 CSV 映射。| 新增 CSV 映射/建立所需的表 |
|出於<blob path>原因排除 Blob:json 模式必須使用 jsonMapping 參數引入| 當沒有提供 json 映射時,Json 攝取。|提供 JSON 對應 |
|下載 Blob 失敗:「遠端伺服器返回錯誤:(404) 找不到。| Blob 不存在。|驗證 Blob 是否存在(如果存在重試)並聯絡 Kusto 團隊 |
|Json 列映射無效:兩個或多個映射元素指向同一列。| JSON 對應式 2 欄位路徑|修復 JSON 對應 |
|引擎錯誤 ─ 【Utilsexception] 引入下載.下載:一個或多個檔案下載失敗(搜尋 KustoLogs<GUID1>的活動ID:<GUID2>, 根活動Id: )| 一個或多個檔案下載失敗。 |重試 |
|剖析失敗:使用 id'<stream name>的串流有格式錯誤的 Csv 格式,每個驗證選項策略失敗 |格式錯誤的 csv 檔(例如,每行的欄數不同)。 僅當驗證策略設置為驗證選項時,才會失敗。 驗證Csv輸入常數列 |檢查您的 csv 檔。 此訊息僅適用於 csv/tsv 檔案 |
|引入用戶端聚合Exception,錯誤消息"缺少有效共用訪問簽名的必需參數" |正在使用的 SAS 是服務,而不是儲存帳戶 |使用儲存帳戶的 SAS |

### <a name="ingestion-error-codes"></a>攝入錯誤代碼

為了説明以程式設計方式處理引入失敗,故障資訊會用數位錯誤代碼(引入錯誤代碼枚舉)來豐富。

|ErrorCode|原因|
|-----------|-------|
|Unknown| 發生未知錯誤|
|Stream_LowMemoryCondition| 操作記憶體不足|
|Stream_WrongNumberOfFields| CSV 文件的欄位數不一致|
|Stream_InputStreamTooLarge| 提交供攝取的文件已超過允許的大小|
|Stream_NoDataToIngest| 找不到要引入的資料串流|
|Stream_DynamicPropertyBagTooLarge| 引入資料中的動態欄包含太多唯一屬性|
|Download_SourceNotFound| 無法從 Azure 儲存下載來源 ─ 找不到來源|
|Download_AccessConditionNotSatisfied| 無法從 Azure 儲存下載來源 - 存取被拒|
|Download_Forbidden| 無法從 Azure 儲存下載來源 - 禁止存取|
|Download_AccountNotFound| 無法從 Azure 儲存下載來源 ─ 找不到帳號|
|Download_BadRequest| 無法從 Azure 儲存下載來源 ─ 錯誤要求|
|Download_NotTransient| 無法從 Azure 儲存下載來源 ─ 不是暫時性錯誤|
|Download_UnknownError| 無法從 Azure 儲存下載來源 ─ 未知錯誤|
|UpdatePolicy_QuerySchemaDoesNotMatchTableSchema| 無法調用更新策略。 查詢架構與表架構不匹配|
|UpdatePolicy_FailedDescendantTransaction| 無法調用更新策略。 失敗的子項目事務更新原則|
|UpdatePolicy_IngestionError| 無法調用更新策略。 發生攝入錯誤|
|UpdatePolicy_UnknownError| 無法調用更新策略。 發生未知錯誤|
|BadRequest_MissingJsonMappingtFailure| Json 模式未引入 jsonMapping 參數|
|BadRequest_InvalidOrEmptyBlob| Blob 不合法或空白 zip 存檔|
|BadRequest_DatabaseNotExist| 資料庫不存在|
|BadRequest_TableNotExist| 表不存在|
|BadRequest_InvalidKustoIdentityToken| 不合法的 kusto 識別碼|
|BadRequest_UriMissingSas| 沒有未知的 Blob 儲存的 SAS 的 Blob 路徑|
|BadRequest_FileTooLarge| 嘗試引入太大的檔案|
|BadRequest_NoValidResponseFromEngine| 從引入命令沒有合法|
|BadRequest_TableAccessDenied| 拒絕存取表|
|BadRequest_MessageExhausted| 訊息已用盡|
|General_BadRequest| 一般錯誤要求 (可能暗示引入非現有資料庫/表)|
|General_InternalServerError| 發生內部伺服器錯誤|

## <a name="detailed-kustoingest-exceptions-reference"></a>詳細的庫斯特.Ingest 異常引用

### <a name="cloudqueuesnotfoundexception"></a>雲佇列找不到異常
當資料管理叢集未返回佇列時引發

基類:[例外](https://msdn.microsoft.com/library/system.exception(v=vs.110).aspx)

欄位：

|名稱|類型|意義
|-----------|----|------------------------------|
|錯誤| `String`| 試著從 DM 檢索佇列時發生的錯誤
                            
其他資訊：

僅當使用[Kusto 排隊引入用戶端](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)時才相關。
在引入過程中,多次嘗試檢索連結到 DM 的 Azure 佇列。 當這些嘗試失敗時,將引發異常,其中包含"錯誤"欄位中的失敗原因,以及"內部異常"欄位中可能存在內部異常。


### <a name="cloudblobcontainersnotfoundexception"></a>雲Blob容器未發現異常
從資料管理叢集未傳回 Blob 容器時引發

基類:[例外](https://msdn.microsoft.com/library/system.exception(v=vs.110).aspx)

欄位：

|名稱|類型|意義       
|-----------|----|------------------------------|
|庫斯托端點| `String`| 相關 DM 的終結點
                            
其他資訊：

僅當使用[Kusto 排隊引入用戶端](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)時才相關。  
引入 Azure 容器中尚未的來源(即檔、DataReader 或 Stream)時,資料將上傳到臨時 Blob 以進行引入。 當找不到要將數據上載到的容器時,將引發異常。

### <a name="duplicateingestionpropertyexception"></a>重複屬性異常
多次設定引入屬性時引發

基類:[例外](https://msdn.microsoft.com/library/system.exception(v=vs.110).aspx)

欄位：

|名稱|類型|意義       
|-----------|----|------------------------------|
|PropertyName| `String`| 重複屬性的名稱
                            
### <a name="postmessagetoqueuefailedexception"></a>訊息後到佇列失敗異常
將訊息發佈到佇列失敗時引發

基類:[例外](https://msdn.microsoft.com/library/system.exception(v=vs.110).aspx)

欄位：

|名稱|類型|意義       
|-----------|----|------------------------------|
|佇列裡| `String`| 佇列的 URI
|錯誤| `String`| 試著發佈到佇列時產生的錯誤訊息
                            
其他資訊：

僅當使用[Kusto 排隊引入用戶端](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)時才相關。  
排隊的客戶端通過將消息上傳到相關的 Azure 佇列來引入數據。 如果帖子失敗,將引發包含佇列URI的異常,即"錯誤"欄位中失敗的原因,以及"內部異常"欄位中可能存在的內部異常。

### <a name="dataformatnotspecifiedexception"></a>資料格式未指定例外
當需要資料格式在引入屬性中沒有指定時引發

基類:引入客戶端異常

其他資訊：

從流引入時,必須在[引入屬性](kusto-ingest-client-reference.md#class-kustoingestionproperties)中指定數據格式,以便正確引入數據。 未指定引入屬性.Format 時引發此異常。

### <a name="invaliduriingestclientexception"></a>不合法客戶端例外
當不合法的 blob URI 作為引入來源提交時引發

基類:引入客戶端異常

### <a name="compressfileingestclientexception"></a>壓縮封存客戶端例外
以引入客戶端無法壓縮為引入的檔案時引發

基類:引入客戶端異常

其他資訊：

檔在引入之前被壓縮。 嘗試壓縮檔失敗時引發此異常。

### <a name="uploadfiletotempblobingestclientexception"></a>上傳檔案到暫存客戶端異常
從引入客戶端無法提供給引入的來源上傳到暫時 Blob 時引發

基類:引入客戶端異常

### <a name="sizelimitexceededingestclientexception"></a>大小限制超出最大用戶端例外
當攝入源過大時引發

基類:引入客戶端異常

欄位：

|名稱|類型|意義       
|-----------|----|------------------------------|
|大小| `long`| 攝入來源大小
|MaxSize| `long`| 允許攝入的最大大小

其他資訊：

如果引入源超過 4GB 的最大大小,則引發異常。 大小驗證可以由[引入屬性類別](kusto-ingest-client-reference.md#class-kustoingestionproperties)的 IgnoreSizeLimit 標誌覆蓋,但不建議[引入大於 1 GB 的單一源](about-kusto-ingest.md#ingestion-best-practices)。

### <a name="uploadfiletotempblobingestclientexception"></a>上傳檔案到暫存客戶端異常
使用引入客戶端無法將引入為引入為暫時的執行的檔案上載到暫時的執行中執行

基類:引入客戶端異常

### <a name="directingestclientexception"></a>定向用戶端例外
執行直接攝入時發生常規錯誤時引發

基類:引入客戶端異常

### <a name="queuedingestclientexception"></a>排用的客戶端例外
執行排隊攝入時出錯時引發

基類:引入客戶端異常

### <a name="ingestclientaggregateexception"></a>引入用戶端集合異常
在攝入過程中發生一個或多個錯誤時引發

基類:[聚合異常](https://msdn.microsoft.com/library/system.aggregateexception(v=vs.110).aspx)

欄位：

|名稱|類型|意義       
|-----------|----|------------------------------|
|攝入錯誤| `IList<IngestClientException>`| 嘗試相機時發生的錯誤以及與這些錯誤相關的來源
|是全域錯誤| `bool`| 指示是否對所有源都發生了異常

## <a name="errors-in-native-code"></a>機器碼中的錯誤
Kusto 引擎是用本機代碼編寫的。 有關本機代碼中的錯誤的詳細資訊,請參閱[本機代碼中的錯誤](../../concepts/errorsinnativecode.md)