---
title: 資料清除-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的資料清除。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: e24edd1f47318d1ee12bfead83d2e09f67a6cc7a
ms.sourcegitcommit: 9fe6ee7db15a5cc92150d3eac0ee175f538953d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2020
ms.locfileid: "82907160"
---
# <a name="data-purge"></a>資料清除

>[!Note]
> 此文章提供如何從裝置或服務刪除個人資料的步驟，並可以用來協助您符合 GDPR 的規範。 如果您要尋找有關 GDPR 的一般資訊，請參閱[服務信任入口網站的 GDPR 一節](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted)。



Azure 資料總管（Kusto）是一種資料平臺，可透過使用和相關的`.purge`命令來支援刪除個別記錄的能力。 您也可以[清除整份資料表](#purging-an-entire-table)。  

> [!WARNING]
> 透過`.purge`命令刪除資料是設計用來保護個人資料，不應用於其他案例。 它不是設計來支援頻繁的刪除要求或刪除大量資料，而且可能會對服務產生顯著的效能影響。

## <a name="purge-guidelines"></a>清除方針

**強烈建議**將個人資料儲存在 Azure 中的小組資料總管只有在仔細設計資料結構描述並調查相關原則之後才會這麼做。

1. 在最佳案例中，此資料上的保留期限會夠短，而且會自動刪除資料。
2. 如果無法使用保留期間，建議的方法是在少數 Kusto 資料表（最佳、只有一個資料表）中，將受限於隱私權規則的所有資料隔離，並從其他所有資料表連結到該值。 如此一來，您就可以在包含敏感性資料的少量資料表上執行資料[清除](#purge-process)程式，並避免所有其他資料表。
3. 呼叫端應每次嘗試批次處理`.purge`命令的執行到每個資料表的1-2 個命令。
   不要發出多個命令，每個都有自己的使用者身分識別述詞;相反地，傳送單一命令，其述詞包含所有需要清除的使用者身分識別。

## <a name="purge-process"></a>清除進程

從 Azure 資料總管選擇性清除資料的程式會在下列步驟中進行：

1. **第1階段：** 假設有 Kusto 資料表名稱和每一記錄述詞指出要刪除哪些記錄，Kusto 會掃描資料表，尋找要找出會參與資料清除的資料分區（有一或多個述詞傳回 true 的記錄）。
2. **第2階段：（虛刪除）** 以沒有述詞傳回 true 之記錄的重新內嵌版本取代資料表中的每個資料分區（以步驟（1）識別）。
   只要沒有任何新資料內嵌到資料表中，此階段的最後查詢就不會再傳回其述詞傳回 true 的資料。 
   「清除虛刪除」階段的持續時間取決於必須清除的記錄數目、其在叢集中的資料分區的分佈、叢集中的節點數目、用於清除作業的備用容量，以及其他數個因素。 階段2的持續時間可能會因幾秒到數小時而有所不同。
3. **第3階段：（實刪除）** 請回復可能含有「有害」資料的所有儲存體成品，並將其從儲存體中刪除。 此階段至少會在前一個階段完成後的5天*後*執行，但在初始命令之後的30天內，才會符合資料隱私權需求。

發出`.purge`命令會觸發此進程，這需要幾天才能完成。 請注意，如果套用述詞的記錄「密度」夠大，此程式將會有效地重新內嵌資料表中的所有資料，因此會對效能和 COGS 造成重大影響。


## <a name="purge-limitations-and-considerations"></a>清除限制和考慮

* **清除程式為最終且無法復原**。 無法「復原」此進程或復原已清除的資料。 因此，[復原資料表](../management/undo-drop-table-command.md)卸載之類的命令無法復原已清除的資料，而將資料復原到先前的版本，則無法移至最新的清除命令。

* 若要避免發生錯誤，建議您在清除之前執行查詢來確認述詞，以確保結果符合預期的結果，或使用會傳回將會清除之預期記錄數目的2步驟進程。 

* 基於預防措施，預設會停用所有叢集上的清除進程。
   啟用清除程式是一次需要開啟[支援票證](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)的一次性作業;請指定您要開啟此`EnabledForPurge`功能。

* 此`.purge`命令會針對資料管理端點執行： `https://ingest-[YourClusterName].kusto.windows.net`。
   此命令需要相關資料庫的[資料庫管理員](../management/access-control/role-based-authorization.md)許可權。 
* 由於清除程式效能的影響，並保證已遵循[清除方針](#purge-guidelines)，因此呼叫者應該修改資料結構描述，讓最少的資料表包含相關的資料，以及每個資料表的批次命令，以減少清除程式的重大 COGS 影響。
* `predicate` [. 清除](#purge-table-tablename-records-command)命令的參數是用來指定要清除的記錄。
`Predicate`大小限制為 63 KB。 在建立時`predicate`：
    * 使用[' in ' 運算子](../query/inoperator.md)，例如`where [ColumnName] in ('Id1', 'Id2', .. , 'Id1000')`。 
        * 請注意[' in ' 運算子](../query/inoperator.md)的限制（清單最多可以包含個`1,000,000`值）。
    * 如果查詢大小很大，請使用[' externaldata ' 運算子](../query/externaldata-operator.md)，例如`where UserId in (externaldata(UserId:string) ["https://...blob.core.windows.net/path/to/file?..."])`。 檔案會儲存要清除的識別碼清單。
        * 在展開所有 externaldata blob （所有 blob 的總大小）之後的查詢大小總計不能超過64MB。 

## <a name="purge-performance"></a>清除效能

在任何指定時間，叢集上只能執行一個清除要求。 所有其他要求都會以「已排程」狀態排入佇列。 必須監視清除要求佇列大小，並將其保留在適當的限制範圍內，以符合您的資料適用的需求。

若要減少清除執行時間：
* 遵循[清除方針](#purge-guidelines)來減少已清除的資料量
* 調整快取[原則](../management/cachepolicy.md)，因為對冷資料進行清除所需的時間較長。
* 相應放大叢集

* 仔細考慮之後，請增加叢集清除容量，如[範圍清除重建容量](../management/capacitypolicy.md#extents-purge-rebuild-capacity)中所述。 變更此參數需要開啟[支援票證](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

## <a name="trigger-the-purge-process"></a>觸發清除進程

> [!Note]
> 清除執行是藉由在資料管理端點上執行 [[清除資料表*TableName*記錄](#purge-table-tablename-records-command)] 命令來叫用（**https://ingest-[YourClusterName]. [region]. kusto**）。

### <a name="purge-table-tablename-records-command"></a>清除資料表*TableName*記錄命令

可能會以兩種方式叫用清除命令，以用於不同的使用案例：
1. 程式設計調用：單一步驟，可供應用程式叫用。 呼叫此命令會直接觸發清除執行順序。

    **語法**

     ```kusto
     // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[region].kusto.windows.net" 
     
     .purge table [TableName] records in database [DatabaseName] with (noregrets='true') <| [Predicate]
     ```

    > [!NOTE]
    > 使用[Kusto 用戶端程式庫](../api/netfx/about-kusto-data.md)NuGet 套件中提供的 CslCommandGenerator API 來產生此命令。

1. 人為調用：需要明確確認做為個別步驟的兩步驟程式。 第一次叫用命令時，會傳回驗證 token，應提供此權杖來執行實際的清除。 此順序可降低不小心刪除不正確資料的風險。 使用此選項可能需要花很長的時間才能在具有大量冷快取資料的大型資料表上完成。
    <!-- If query times-out on DM endpoint (default timeout is 10 minutes), it is recommended to use the [engine `whatif` command](#purge-whatif-command) directly againt the engine endpoint while increasing the [server timeout limit](../concepts/querylimits.md#limit-on-request-execution-time-timeout). Only after you have verified the expected results using the engine whatif command, issue the purge command via the DM endpoint using the 'noregrets' option. -->

     **語法**

     ```kusto
     // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[region].kusto.windows.net" 
     
     // Step #1 - retrieve a verification token (no records will be purged until step #2 is executed)
     .purge table [TableName] records in database [DatabaseName] <| [Predicate]

     // Step #2 - input the verification token to execute purge
     .purge table [TableName] records in database [DatabaseName] with (verificationtoken='<verification token from step #1>') <| [Predicate]
     ```
    
    |參數  |描述  |
    |---------|---------|
    | DatabaseName   |   資料庫名稱      |
    | TableName     |     資料表的名稱    |
    | Predicate    |    識別要清除的記錄。 請參閱下方的清除述詞限制。 | 
    | noregrets    |     如果設定，則會觸發單一步驟啟用。    |
    | verificationtoken     |  在雙步驟啟用案例中（未設定**noregrets** ），此權杖可以用來執行第二個步驟並認可動作。 如果未指定**verificationtoken** ，它會觸發命令的第一個步驟，其中會傳回有關清除的資訊，以及應傳回給命令的權杖，以執行步驟 #2。   |

    **清除述詞限制**
    * 述詞必須是簡單的選取專案（例如 *，其中 [columnname] = = ' X '* / ，*其中 [columnname] 位於（' X '、' Y '、' Z '）和 [OtherColumn] = = ' a '*）。
    * 多個篩選準則必須與 ' and ' 結合，而不是`where`個別的子句（ `where [ColumnName] == 'X' and  OtherColumn] == 'Y'`例如和`where [ColumnName] == 'X' | where [OtherColumn] == 'Y'`not）。
    * 述詞不能參考要清除之資料表（*TableName*）以外的資料表。 述詞只能包含選取範圍語句（`where`）。 它無法從資料表投影特定資料行（執行 '* `table` | 時的輸出架構）述詞 ' 必須*符合資料表架構）。
    * 不支援系統函數（例如`ingestion_time()`、 `extent_id()`、）做為述詞的一部分。

#### <a name="example-two-step-purge"></a>範例：雙步驟清除

1. 若要在雙步驟啟用案例中起始清除，請執行命令的步驟 #1：

    ```kusto
    // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[region].kusto.windows.net" 
     
    .purge table MyTable records in database MyDatabase <| where CustomerId in ('X', 'Y')
    ```

    **輸出**

    |NumRecordsToPurge |EstimatedPurgeExecutionTime| VerificationToken
    |--|--|--
    |1596 |00:00:02 |e43c7184ed22f4f23c7a9d7b124d196be2e570096987e5baadf65057fa65736b

    執行步驟 #2 之前，請先驗證 NumRecordsToPurge。 
2. 若要在雙步驟啟用案例中完成清除，請使用步驟 #1 傳回的驗證權杖來執行步驟 #2：

    ```kusto
    .purge table MyTable records in database MyDatabase
    with (verificationtoken='e43c7184ed22f4f23c7a9d7b124d196be2e570096987e5baadf65057fa65736b')
    <| where CustomerId in ('X', 'Y')
    ```

    **輸出**

    |OperationId |DatabaseName |TableName |ScheduledTime |Duration |LastUpdatedOn |EngineOperationId |狀況 |StateDetails |EngineStartTime |EngineDuration |重試 |ClientRequestId |主體
    |--|--|--|--|--|--|--|--|--|--|--|--|--|--
    |c9651d74-3b80-4183-90bb-bbe9e42eadc4 |MyDatabase |MyTable |2019-01-20 11：41：05.4391686 |00：00：00.1406211 |2019-01-20 11：41：05.4391686 | |排程 | | | |0 |KE.RunCommand; 1d0ad28b-f791-4f5a-a60f-0e32318367b7 |AAD 應用程式識別碼 = .。。

#### <a name="example-single-step-purge"></a>範例：單一步驟清除

若要在單一步驟啟用案例中觸發清除，請執行下列命令：

    ```kusto
    // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[region].kusto.windows.net" 
     
    .purge table MyTable records in database MyDatabase with (noregrets='true') <| where CustomerId in ('X', 'Y')
    ```

**輸出**

|OperationId |DatabaseName |TableName |ScheduledTime |Duration |LastUpdatedOn |EngineOperationId |狀況 |StateDetails |EngineStartTime |EngineDuration |重試 |ClientRequestId |主體
|--|--|--|--|--|--|--|--|--|--|--|--|--|--
|c9651d74-3b80-4183-90bb-bbe9e42eadc4 |MyDatabase |MyTable |2019-01-20 11：41：05.4391686 |00：00：00.1406211 |2019-01-20 11：41：05.4391686 | |排程 | | | |0 |KE.RunCommand; 1d0ad28b-f791-4f5a-a60f-0e32318367b7 |AAD 應用程式識別碼 = .。。

### <a name="cancel-purge-operation-command"></a>取消清除操作命令

如有需要，您可以解除擱置中的清除要求。

> [!NOTE]
> 此作業適用于錯誤復原案例。 不保證會成功，而且不應該是正常操作流程的一部分。 它只能套用至佇列中的要求（尚未分派至引擎節點進行執行）。 此命令會在資料管理端點上執行。

**語法**

```kusto
 .cancel purge <OperationId>
 ```

**範例**

```kusto
 .cancel purge aa894210-1c60-4657-9d21-adb2887993e1
 ```

**輸出**

此命令的輸出與「顯示清除*OperationId*」命令輸出相同，顯示取消的清除作業已更新的狀態。 如果嘗試成功，作業狀態會更新為「已放棄」，否則作業狀態不會改變。 

|OperationId |DatabaseName |TableName |ScheduledTime |Duration |LastUpdatedOn |EngineOperationId |狀況 |StateDetails |EngineStartTime |EngineDuration |重試 |ClientRequestId |主體
|--|--|--|--|--|--|--|--|--|--|--|--|--|--
|c9651d74-3b80-4183-90bb-bbe9e42eadc4 |MyDatabase |MyTable |2019-01-20 11：41：05.4391686 |00：00：00.1406211 |2019-01-20 11：41：05.4391686 | |Abandoned | | | |0 |KE.RunCommand; 1d0ad28b-f791-4f5a-a60f-0e32318367b7 |AAD 應用程式識別碼 = .。。

## <a name="track-purge-operation-status"></a>追蹤清除操作狀態 

> [!Note]
> 清除作業可以使用 [[顯示清除](#show-purges-command)] 命令來追蹤，針對資料管理端點執行（**https://ingest-[YourClusterName]. [region]. kusto**）。

狀態 = 「已完成」表示清除作業的第一個階段成功完成，也就是已虛刪除記錄，而且無法再供查詢使用。 客戶**不**需要追蹤和驗證第二個階段（實刪除）完成。 Kusto 會在內部監視此階段。

### <a name="show-purges-command"></a>顯示清除命令

`Show purges`命令會在要求的時段內指定作業識別碼，以顯示清除作業狀態。 

```kusto
.show purges <OperationId>
.show purges [in database <DatabaseName>]
.show purges from '<StartDate>' [in database <DatabaseName>]
.show purges from '<StartDate>' to '<EndDate>' [in database <DatabaseName>]
```

|屬性  |描述  |強制/選擇性
|---------|---------|
|OperationId    |      執行單一階段或第二個階段之後，資料管理作業識別碼 outputed。   |強制性
|StartDate    |   篩選作業的時間限制下限。 如果省略，則預設為目前時間的24小時。      |選用
|EndDate    |  篩選作業的時間上限。 如果省略，則預設為目前時間。       |選用
|DatabaseName    |     用來篩選結果的資料庫名稱。    |選用

> [!NOTE]
> 只有在用戶端具有[資料庫管理員](../management/access-control/role-based-authorization.md)許可權的資料庫上才會提供狀態。

**範例**

```kusto
.show purges
.show purges c9651d74-3b80-4183-90bb-bbe9e42eadc4
.show purges from '2018-01-30 12:00'
.show purges from '2018-01-30 12:00' to '2018-02-25 12:00'
.show purges from '2018-01-30 12:00' to '2018-02-25 12:00' in database MyDatabase
```

**輸出** 

|OperationId |DatabaseName |TableName |ScheduledTime |Duration |LastUpdatedOn |EngineOperationId |狀況 |StateDetails |EngineStartTime |EngineDuration |重試 |ClientRequestId |主體
|--|--|--|--|--|--|--|--|--|--|--|--|--|--
|c9651d74-3b80-4183-90bb-bbe9e42eadc4 |MyDatabase |MyTable |2019-01-20 11：41：05.4391686 |00：00：33.6782130 |2019-01-20 11：42：34.6169153 |a0825d4d-6b0f-47f3-a499-54ac5681ab78 |Completed |已成功完成清除（儲存體成品待刪除） |2019-01-20 11：41：34.6486506 |00：00：04.4687310 |0 |KE.RunCommand; 1d0ad28b-f791-4f5a-a60f-0e32318367b7 |AAD 應用程式識別碼 = .。。

* **OperationId** -執行清除時傳回的 DM 作業識別碼。 
* **DatabaseName** -資料庫名稱（區分大小寫）。 
* **TableName** -資料表名稱（區分大小寫）。 
* 對 DM 服務執行 [清除] 命令的**ScheduledTime**時間。 
* **持續時間**-清除作業的總持續時間，包括執行 DM 佇列等候時間。 
* **EngineOperationId** -在引擎中執行之實際清除作業的作業識別碼。 
* **狀態**-清除狀態，可以是下列其中一項： 
    * 排程-清除作業已排程執行。 如果工作保持**排定**，可能會有清除作業的待處理專案。 若要清除此待處理專案，請參閱[清除效能](#purge-performance)。 如果清除作業因暫時性錯誤而失敗，DM 將會重試此作業，並再次設定為 [**已排程]** （因此您可能會看到作業從 [已**排程**] 轉換為 [執行中] **，然後回到**[已**排程**]）。
    * InProgress-清除 operaration 正在引擎中進行。 
    * 已完成-已成功完成清除。
    * BadInput-清除錯誤的輸入失敗，將不會重試。 這可能是因為各種問題，例如述詞中的語法錯誤、清除命令的無效述詞、超出限制的查詢（例如，externaldata 操作員中的1百萬個實體，或超過總擴充查詢大小的64MB），以及 externaldata blob 的404或403錯誤。
    * 失敗-清除失敗，將不會重試。 如果作業在佇列中等待的時間太長（超過14天），這可能是因為其他清除作業的待處理專案，或超過重試限制的失敗數而發生。 後者會引發內部監視警示，並會由 Azure 資料總管小組進行調查。 
* StateDetails-狀態的描述。
* EngineStartTime-發出命令給引擎的時間。 如果這段時間與 ScheduledTime 之間有很大的差異，通常會有一個重大的清除作業待處理專案，而叢集並不會跟上步調。 
* 引擎中實際清除執行的 EngineDuration 時間。 如果重新嘗試清除多次，這就是所有執行持續時間的總和。 
* 重試次數： DM 服務因暫時性錯誤而重試作業的次數。
* ClientRequestId-DM 清除要求的用戶端活動識別碼。 
* 主體-清除命令簽發者的身分識別。

## <a name="purging-an-entire-table"></a>清除整個資料表
清除資料表包含卸載資料表，並將其標示為已清除，讓[清除](#purge-process)程式中所述的實刪除程式可在其上執行。 卸載資料表而不清除它，並不會刪除它的所有儲存成品（根據最初設定于資料表上的固定保留原則來刪除）。 此`purge table allrecords`命令既快速又有效率，而且如果適用于您的案例，清除記錄處理常式會更理想。 

> [!Note]
> 此命令的叫用方式是在資料管理端點上執行[清除資料表*TableName* allrecords](#purge-table-tablename-allrecords-command)命令（**https://ingest-[YourClusterName]. [region]. kusto**）。

### <a name="purge-table-tablename-allrecords-command"></a>清除資料表*TableName* allrecords 命令

類似于「[清除資料表記錄](#purge-table-tablename-records-command)」命令，可以在程式設計（單一步驟）或手動（雙步驟）模式中叫用此命令。
1. 程式設計調用（單一步驟）：

     **語法**

     ```kusto
     // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[region].kusto.windows.net" 
     
     .purge table [TableName] in database [DatabaseName] allrecords with (noregrets='true')
     ```

2. 人類調用（兩個步驟）：

     **語法**

     ```kusto
     // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[region].kusto.windows.net" 
     
     // Step #1 - retrieve a verification token (the table will not be purged until step #2 is executed)
     .purge table [TableName] in database [DatabaseName] allrecords

     // Step #2 - input the verification token to execute purge
     .purge table [TableName] in database [DatabaseName] allrecords with (verificationtoken='<verification token from step #1>')
     ```

    |參數  |描述  |
    |---------|---------|
    |**DatabaseName**   |   資料庫的名稱。      |
    |**TableName**     |     資料表的名稱。    |
    |**noregrets**    |     如果設定，則會觸發單一步驟啟用。    |
    |**verificationtoken**     |  在雙步驟啟用案例中（未設定**noregrets** ），此權杖可以用來執行第二個步驟並認可動作。 如果未指定**verificationtoken** ，它會觸發命令的第一個步驟，其中會傳回權杖，以傳回命令並執行命令的步驟 #2。|

#### <a name="example-two-step-purge"></a>範例：雙步驟清除

1. 若要在雙步驟啟用案例中起始清除，請執行命令的步驟 #1： 

    ```kusto
    // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[region].kusto.windows.net" 
     
    .purge table MyTable in database MyDatabase allrecords
    ```

    **輸出**

    | VerificationToken
    |--
    |e43c7184ed22f4f23c7a9d7b124d196be2e570096987e5baadf65057fa65736b

1.  若要在雙步驟啟用案例中完成清除，請使用步驟 #1 傳回的驗證權杖來執行步驟 #2：

    ```kusto
    .purge table MyTable in database MyDatabase allrecords 
    with (verificationtoken='eyJTZXJ2aWNlTmFtZSI6IkVuZ2luZS1pdHNhZ3VpIiwiRGF0YWJhc2VOYW1lIjoiQXp1cmVTdG9yYWdlTG9ncyIsIlRhYmxlTmFtZSI6IkF6dXJlU3RvcmFnZUxvZ3MiLCJQcmVkaWNhdGUiOiIgd2hlcmUgU2VydmVyTGF0ZW5jeSA9PSAyNSJ9')
    ```
    
    輸出與 '. show tables ' 命令輸出相同（未清除的資料表傳回）。

    **輸出**

    |TableName|DatabaseName|資料夾|DocString
    |---|---|---|---
    |OtherTable|MyDatabase|---|---


#### <a name="example-single-step-purge"></a>範例：單一步驟清除

若要在單一步驟啟用案例中觸發清除，請執行下列命令：

```kusto
// Connect to the Data Management service
#connect "https://ingest-[YourClusterName].[region].kusto.windows.net" 

.purge table MyTable in database MyDatabase allrecords with (noregrets='true')
```

輸出與 '. show tables ' 命令輸出相同（未清除的資料表傳回）。

**輸出**

|TableName|DatabaseName|資料夾|DocString
|---|---|---|---
|OtherTable|MyDatabase|---|---


