---
title: 資料清除 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的數據清除。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 49d024d1deecd8e0c7bf16eda9917cd237fe6319
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523268"
---
# <a name="data-purge"></a>資料清除

>[!Note]
> 本文提供如何從裝置或服務上刪除個人資料的步驟，而且可以用來支援 GDPR 的義務。 如果您要尋找有關 GDPR 的一般資訊,請參閱[服務信任門戶的 GDPR 部分](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted)。



作為數據平臺,Azure 資料資源管理員 (Kusto)`.purge`支援透過使用和相關命令刪除單個記錄的功能。 您可以[清除整個表格](#purging-an-entire-table)。  

> [!WARNING]
> 通過`.purge`該命令刪除數據旨在保護個人數據,不應在其他方案中使用。 它不用於支援頻繁的刪除請求或刪除大量數據,並且可能對服務產生顯著的性能影響。

## <a name="purge-guidelines"></a>清除指南

**強烈建議在**Azure 資料資源管理器中儲存個人數據的團隊只有在仔細設計數據架構並調查相關策略後才能執行此操作。

1. 在最佳情況下,此數據的保留期足夠短,數據將自動刪除。
2. 如果無法使用保留期,建議的方法是在少量 Kusto 表(最好,只有一個表)中隔離受隱私規則約束的所有數據,並將其與所有其他表連結。 這允許在少數包含敏感數據的表上運行數據[清除過程](#purge-process),並避免所有其他表。
3. 調用方應嘗試將`.purge`命令的執行批處理到每天每表 1-2 個命令。
   不要發出多個命令,每個命令都有自己的用戶標識謂詞;而是發送一個命令,其謂詞包括需要清除的所有用戶標識。

## <a name="purge-process"></a>清除過程

有選擇地清除 Azure 資料資源管理員中的資料的過程在以下步驟中發生:

1. **第一階段:** 給定 Kusto 表名稱和每個記錄謂詞,指示要刪除哪些記錄,Kusto 會掃描表,以查找標識將參與資料清除的數據分片(具有一個或多個謂詞返回 true 的記錄)。
2. **第二階段(軟刪除)** 將表中的每個資料分片(在步驟 (1) 中識別)替換為重新引入的版本,該版本沒有謂詞返回 true 的記錄。
   只要沒有新數據被引入到表中,在此階段結束時查詢將不再返回謂詞返回 true 的數據。 
   清除軟刪除階段的持續時間取決於必須清除的記錄數、其在群集中的數據分片中的分佈、群集中的節點數、清除操作的備用容量以及若干其他因素。 階段2的持續時間可能從幾秒鐘到數小時不等。
3. **第 3 階段:(硬刪除)** 重新處理可能具有"有害"資料的所有存儲專案,並從存儲中刪除它們。 此階段在前一階段完成後至少 5*天執行*,但不超過初始命令後 30 天,以符合數據隱私要求。

發出`.purge`命令會觸發此過程,此過程需要幾天才能完成。 請注意,如果謂詞應用的記錄的"密度"足夠大,則該過程將有效地重新引入表中的所有數據,從而對性能和 COGS 產生重大影響。


## <a name="purge-limitations-and-considerations"></a>清除限制與注意事項

* **清除過程是最終的與不可逆轉的**。 無法「撤消」此過程或恢復已清除的數據。 因此,[復原表刪除](../management/undo-drop-table-command.md)等命令無法恢復清除的資料,並且將資料回滾到以前的版本不能轉到「之前」最新的清除命令。

* 為了避免錯誤,建議在清除之前運行查詢來驗證謂詞,以確保結果與預期結果匹配,或者使用返回將清除的預期記錄數的兩步過程。 

* 作為預防措施,默認情況下,在所有群集上禁用清除過程。
   啟用清除過程是一次性操作,需要打開[支援票證](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview);請指定要打開該`EnabledForPurge`功能。

* 這個`.purge`指令管理時執行: `https://ingest-[YourClusterName].kusto.windows.net`
   該命令需要相關[資料庫的資料庫管理員](../management/access-control/role-based-authorization.md)許可權。 
* 由於清除過程性能的影響,並且為了保證[已遵循清除準則](#purge-guidelines),調用方應修改資料架構,以便最小表包含相關資料,以及每個表的批處理命令,以減少清除過程對 COGS 的重大影響。
* `predicate` [.purge](#purge-table-tablename-records-command)命令的參數用於指定要清除的記錄。
`Predicate`大小限制為 63 KB。 建構`predicate`時 :
    * 使用['in'運算子](../query/inoperator.md),例如`where [ColumnName] in ('Id1', 'Id2', .. , 'Id1000')`。 
        * 請注意[「in」運算子](../query/inoperator.md)的限制(清單最多可以包含`1,000,000`值)。
    * 如果查詢大小較大,請使用[「外部資料」 運算子](../query/externaldata-operator.md)`where UserId in (externaldata(UserId:string) ["https://...blob.core.windows.net/path/to/file?..."])`, 例如 。 該檔案儲存要清除的已清除的已清除的已包含的已清單。
        * 展開所有外部資料 blob(所有 Blob 的總大小)后的總查詢大小不能超過 64MB。 

## <a name="purge-performance"></a>清除效能

在任何給定時間,只能在群集上執行一個清除請求。 所有其他請求都以"計劃"狀態排隊。 必須監視清除請求佇列大小,並將其保持在適當的限制內,以匹配適用於您的數據的要求。

要減少清除執行時間:
* 以[清除的資料](#purge-guidelines)量
* 調整[緩存策略](../management/cachepolicy.md),因為清除在冷數據上需要更長的時間。
* 橫向延伸叢集

* 在仔細考慮後增加群集清除容量,詳見[範圍清除重建容量](../management/capacitypolicy.md#extents-purge-rebuild-capacity)。 變更此參數需要開啟[支援票證](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

## <a name="trigger-the-purge-process"></a>觸發清除程序

> [!Note]
> 通過在資料管理終結點**https://ingest-([您的ClusterName]kusto.windows.net**上執行[清除*表表Name*記錄](#purge-table-tablename-records-command)命令)調用清除執行。

### <a name="purge-table-tablename-records-command"></a>清除*表格名稱*記錄指令

對於不同的使用方案,可以通過兩種方式調用清除命令:
1. 程式設計調用:應用程式要調用的單步執行。 呼叫此命令會直接觸發清除執行序列。

    **語法**
     ```kusto
     .purge table [TableName] records in database [DatabaseName] with (noregrets='true') <| [Predicate]
     ```

    > [!NOTE]
    > 使用作為[Kusto 用戶端庫](../api/netfx/about-kusto-data.md)NuGet 包一部分的 CslCommandGenerator API 生成此命令。

1. 人工調用:需要顯式確認作為單獨步驟的兩步過程。 命令的首次調用返回驗證權杖,該令牌應提供以運行實際清除。 此序列降低了無意中刪除錯誤數據的風險。 使用此選項可能需要很長時間才能完成具有大量冷緩存數據的大型表。
    <!-- If query times-out on DM endpoint (default timeout is 10 minutes), it is recommended to use the [engine `whatif` command](#purge-whatif-command) directly againt the engine endpoint while increasing the [server timeout limit](../concepts/querylimits.md#limit-on-request-execution-time-timeout). Only after you have verified the expected results using the engine whatif command, issue the purge command via the DM endpoint using the 'noregrets' option. -->

     **語法**
     ```kusto
     // Step #1 - retrieve a verification token (no records will be purged until step #2 is executed)
     .purge table [TableName] records in database [DatabaseName] <| [Predicate]

     // Step #2 - input the verification token to execute purge
     .purge table [TableName] records in database [DatabaseName] with (verificationtoken='<verification token from step #1>') <| [Predicate]
     ```
    
    |參數  |描述  |
    |---------|---------|
    | DatabaseName   |   資料庫名稱      |
    | TableName     |     資料表的名稱    |
    | Predicate    |    標識要清除的記錄。 請參閱下面的清除謂詞限制。 | 
    | 無悔    |     如果設置,將觸發單步驟啟動。    |
    | 驗證權杖     |  在兩步啟動方案中(未設置**無遺憾**),此令牌可用於執行第二步並提交操作。 如果未指定**驗證令牌**,它將觸發命令的第一步,其中返回有關清除的資訊和令牌,該標記應傳回命令以執行步驟#2。   |

    **清除謂詞限制**
    * 謂詞必須是一個簡單的選擇(例如 *[列名] = "X",* / *其中 [列名] 在其中("X","Y","Z")和 [其他列] = "A")。*
    * 多個篩選器必須與 「和」結合,而不是單獨`where`的子句(`where [ColumnName] == 'X' and  OtherColumn] == 'Y'`例如,`where [ColumnName] == 'X' | where [OtherColumn] == 'Y'`而不是)。
    * 謂詞不能引用清除表以外的表(*表名*)。 謂詞只能包括選擇語句`where`( 。 無法投影表中的特定欄(執行時輸出架構*`table`* |謂詞*' 必須與表架構匹配)。
    * 系統函數(如`ingestion_time()``extent_id()`, ) 不支援作為謂詞的一部分。

#### <a name="example-two-step-purge"></a>範例:兩步清除

1. 要在兩步啟動方案中啟動清除,#1命令运行步骤:

    ```kusto
    .purge table MyTable records in database MyDatabase <| where CustomerId in ('X', 'Y')
    ```

    **輸出**

    |數位記錄清除 |估計清除執行時間| 驗證權杖
    |--|--|--
    |1,596 |00:00:02 |e43c7184ed22f4f23c7a9d7b124d196be2e57009687e5baadf65057fa65736b

    在運行步驟#2之前驗證 NumRecords 到清除。 
2. 要在兩步啟動方案中完成清除,請使用步驟#1返回的驗證令牌運行步驟#2:

    ```kusto
    .purge table MyTable records in database MyDatabase
    with (verificationtoken='e43c7184ed22f4f23c7a9d7b124d196be2e570096987e5baadf65057fa65736b')
    <| where CustomerId in ('X', 'Y')
    ```

    **輸出**

    |操作代碼 |DatabaseName |TableName |預定時間 |Duration |上次更新時間 |發動機操作 Id |State |狀態詳細資訊 |發動機啟動時間 |發動機持續時間 |重試 |ClientRequestId |主體
    |--|--|--|--|--|--|--|--|--|--|--|--|--|--
    |c9651d74-3b80-4183-90bb-bbe9e42eadc4 |我的資料庫 |MyTable |2019-01-20 11:41:05.4391686 |00:00:00.1406211 |2019-01-20 11:41:05.4391686 | |已排程 | | | |0 |科。RunCommand;1d0ad28b-f791-4f5a-a60f-0e32318367b7 |AAD 應用程式 id_...

#### <a name="example-single-step-purge"></a>範例:單步驟清除

要在單步驟啟動方案中觸發清除,執行以下命令:

```kusto
.purge table MyTable records in database MyDatabase with (noregrets='true') <| where CustomerId in ('X', 'Y')
```

**輸出**

|操作代碼 |DatabaseName |TableName |預定時間 |Duration |上次更新時間 |發動機操作 Id |State |狀態詳細資訊 |發動機啟動時間 |發動機持續時間 |重試 |ClientRequestId |主體
|--|--|--|--|--|--|--|--|--|--|--|--|--|--
|c9651d74-3b80-4183-90bb-bbe9e42eadc4 |我的資料庫 |MyTable |2019-01-20 11:41:05.4391686 |00:00:00.1406211 |2019-01-20 11:41:05.4391686 | |已排程 | | | |0 |科。RunCommand;1d0ad28b-f791-4f5a-a60f-0e32318367b7 |AAD 應用程式 id_...

### <a name="cancel-purge-operation-command"></a>取消清除操作指令

如果需要,可以取消掛起的清除請求。

> [!NOTE]
> 此操作適用於錯誤恢復方案。 它不能保證成功,也不應該是正常操作流的一部分。 它只能應用於佇列內請求(尚未調度到引擎節點執行)。 該命令在數據管理終結點上執行。

**語法**

```kusto
 .cancel purge <OperationId>
 ```

**範例**

```kusto
 .cancel purge aa894210-1c60-4657-9d21-adb2887993e1
 ```

**輸出**

此命令的輸出與"顯示清除*操作 Id"* 指令輸出相同,顯示正在取消的清除操作的更新狀態。 如果嘗試成功,操作狀態將更新為"已放棄",否則操作狀態不會更改。 

|操作代碼 |DatabaseName |TableName |預定時間 |Duration |上次更新時間 |發動機操作 Id |State |狀態詳細資訊 |發動機啟動時間 |發動機持續時間 |重試 |ClientRequestId |主體
|--|--|--|--|--|--|--|--|--|--|--|--|--|--
|c9651d74-3b80-4183-90bb-bbe9e42eadc4 |我的資料庫 |MyTable |2019-01-20 11:41:05.4391686 |00:00:00.1406211 |2019-01-20 11:41:05.4391686 | |Abandoned | | | |0 |科。RunCommand;1d0ad28b-f791-4f5a-a60f-0e32318367b7 |AAD 應用程式 id_...

## <a name="track-purge-operation-status"></a>追蹤清除操作狀態 

> [!Note]
> 可以使用[顯示清除](#show-purges-command)命令追蹤清除操作,該命令針對資料管理終結點**https://ingest-([您的群集名稱]kusto.windows.net)** 執行。

狀態 = "已完成"表示清除操作的第一階段成功完成,即記錄是軟刪除的,並且不再可用於查詢。 客戶**不應**追蹤和驗證第二階段(硬刪除)完成情況。 庫斯托內部監視此階段。

### <a name="show-purges-command"></a>顯示清除命令

`Show purges`命令通過在請求的時間段內指定操作 ID 來顯示清除操作狀態。 

```kusto
.show purges <OperationId>
.show purges [in database <DatabaseName>]
.show purges from '<StartDate>' [in database <DatabaseName>]
.show purges from '<StartDate>' to '<EndDate>' [in database <DatabaseName>]
```

|屬性  |描述  |強制性/選擇
|---------|---------|
|操作代碼    |      數據管理操作 Id 在執行單階段或第二階段後輸出。   |強制性
|StartDate    |   篩選操作的時間限制較低。 如果省略,則預設為當前時間之前 24 小時。      |選用
|EndDate    |  篩選操作的上限。 如果省略,則預設為當前時間。       |選用
|DatabaseName    |     資料庫名稱以篩選結果。    |選用

> [!NOTE]
> 狀態將僅在用戶端具有[資料庫管理](../management/access-control/role-based-authorization.md)許可權的資料庫上提供。

**範例**

```kusto
.show purges
.show purges c9651d74-3b80-4183-90bb-bbe9e42eadc4
.show purges from '2018-01-30 12:00'
.show purges from '2018-01-30 12:00' to '2018-02-25 12:00'
.show purges from '2018-01-30 12:00' to '2018-02-25 12:00' in database MyDatabase
```

**輸出** 

|操作代碼 |DatabaseName |TableName |預定時間 |Duration |上次更新時間 |發動機操作 Id |State |狀態詳細資訊 |發動機啟動時間 |發動機持續時間 |重試 |ClientRequestId |主體
|--|--|--|--|--|--|--|--|--|--|--|--|--|--
|c9651d74-3b80-4183-90bb-bbe9e42eadc4 |我的資料庫 |MyTable |2019-01-20 11:41:05.4391686 |00:00:33.6782130 |2019-01-20 11:42:34.6169153 |a0825d4d-6b0f-47f3-a499-54ac5681ab78 |Completed |清除成功完成(儲存待刪除的儲存專案) |2019-01-20 11:41:34.6486506 |00:00:04.4687310 |0 |科。RunCommand;1d0ad28b-f791-4f5a-a60f-0e32318367b7 |AAD 應用程式 id_...

* **操作 Id** - 執行清除時傳回的 DM 操作 ID。 
* **資料庫名稱**- 資料庫名稱(區分大小寫)。 
* **表名稱**- 表名稱(區分大小寫)。 
* **計劃時間**- 向 DM 服務執行清除命令的時間。 
* **持續時間**- 清除操作的總持續時間,包括執行 DM 佇列等待時間。 
* **發動機操作 Id** - 在引擎中執行的實際清除的操作 ID。 
* **狀態**- 清除狀態,可以是以下狀態之一: 
    * 計劃 - 已計劃執行清除操作。 如果作業保持**計劃**狀態,則清除操作可能積壓。 請參閱[清除性能](#purge-performance)以清除此積壓工作。 如果清除操作在暫時性錯誤時失敗,則 DM 將重試它並再次設定為 **「計畫**」(因此,您可能會看到從 **「計畫**」到 **「未定進度**」並返回到**計畫的**操作轉換)。
    * 進度 - 清除操作在引擎中正在進行。 
    * 已完成 - 已成功清除。
    * 錯誤輸入 - 清除錯誤輸入失敗,不會重試。 這可能是由於各種問題,如謂詞中的語法錯誤、清除命令的非法謂詞、超出限制的查詢(例如,外部數據運算符中的超過 1M 實體或總擴展查詢大小超過 64MB)以及外部數據 blob 的 404 或 403 錯誤。
    * 清除失敗,不會重試。 如果操作在佇列中等待時間過長(超過 14 天),則可能會發生這種情況,因為其他清除操作積壓或多個故障超過重試限制。 後者將引發內部監視警報,並由 Azure 數據資源管理器團隊進行調查。 
* 國家詳情 - 國家描述。
* 發動機開始時間 - 命令頒發給引擎的時間。 如果這次與計劃時間有很大區別,則清除操作通常存在大量積壓,並且群集跟不上速度。 
* 發動機持續時間 - 發動機中實際清除執行的時間。 如果多次重試清除,則它是所有執行持續時間的總和。 
* 重試 - DM 服務由於暫時性錯誤而重試操作的次數。
* 用戶端請求Id - DM 清除請求的用戶端活動 ID。 
* 主體 - 清除命令頒發者的身份。



## <a name="purging-an-entire-table"></a>清除整個表格
清除表包括刪除表,並將其標記為清除,以便[清除過程中](#purge-process)描述的硬刪除過程在表上運行。 在不清除表的情況下刪除表不會刪除其所有儲存項目(根據最初在表上設置的硬保留策略刪除)。 該`purge table allrecords`命令快速而高效,並且比清除記錄過程更可取(如果適用於您的方案)。 

> [!Note]
> 該命令通過執行[清除*表表名稱*](#purge-table-tablename-allrecords-command)資料管理終結點**https://ingest-([您的ClusterName].kusto.windows.net)** 上的所有記錄命令來調用。

### <a name="purge-table-tablename-allrecords-command"></a>清除表*格名稱*所有紀錄指令

與['.purge 表記錄](#purge-table-tablename-records-command)'命令類似,可以在程式設計(單步)或手動(兩步)模式下調用此命令。
1. 程式設計呼叫(單步驟):

     **語法**
     ```kusto
     .purge table [TableName] in database [DatabaseName] allrecords with (noregrets='true')
     ```

2. 人類調用(兩個步驟):

     **語法**
     ```kusto
     // Step #1 - retrieve a verification token (the table will not be purged until step #2 is executed)
     .purge table [TableName] in database [DatabaseName] allrecords

     // Step #2 - input the verification token to execute purge
     .purge table [TableName] in database [DatabaseName] allrecords with (verificationtoken='<verification token from step #1>')
     ```

    |參數  |描述  |
    |---------|---------|
    |**DatabaseName**   |   資料庫的名稱。      |
    |**表格名稱**     |     資料表的名稱。    |
    |**無悔**    |     如果設置,將觸發單步驟啟動。    |
    |**驗證權杖**     |  在兩步啟動方案中(未設置**無遺憾**),此令牌可用於執行第二步並提交操作。 如果未指定**驗證權杖**,它將觸發命令的第一步(其中返回令牌)以傳遞回命令並執行命令#2步驟。|

#### <a name="example-two-step-purge"></a>範例:兩步清除

1. 要在兩步啟動方案中啟動清除,#1命令运行步骤: 

    ```kusto
    .purge table MyTable in database MyDatabase allrecords
    ```

    **輸出**

    | 驗證權杖
    |--
    |e43c7184ed22f4f23c7a9d7b124d196be2e57009687e5baadf65057fa65736b

1.  要在兩步啟動方案中完成清除,請使用步驟#1返回的驗證令牌運行步驟#2:

    ```kusto
    .purge table MyTable in database MyDatabase allrecords 
    with (verificationtoken='eyJTZXJ2aWNlTmFtZSI6IkVuZ2luZS1pdHNhZ3VpIiwiRGF0YWJhc2VOYW1lIjoiQXp1cmVTdG9yYWdlTG9ncyIsIlRhYmxlTmFtZSI6IkF6dXJlU3RvcmFnZUxvZ3MiLCJQcmVkaWNhdGUiOiIgd2hlcmUgU2VydmVyTGF0ZW5jeSA9PSAyNSJ9')
    ```
    輸出與".show 表"命令輸出相同(傳回時不清除表)。

    **輸出**

    |TableName|DatabaseName|資料夾|文件字串
    |---|---|---|---
    |其他表|我的資料庫|---|---


#### <a name="example-single-step-purge"></a>範例:單步驟清除

要在單步驟啟動方案中觸發清除,執行以下命令:

```kusto
.purge table MyTable in database MyDatabase allrecords with (noregrets='true')
```

輸出與".show 表"命令輸出相同(傳回時不清除表)。

**輸出**

|TableName|DatabaseName|資料夾|文件字串
|---|---|---|---
|其他表|我的資料庫|---|---


