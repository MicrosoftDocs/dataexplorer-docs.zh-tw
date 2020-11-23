---
title: 要求屬性和 ClientRequestProperties-Azure 資料總管
description: 本文說明 Azure 資料總管中的要求屬性和 ClientRequestProperties。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/23/2019
ms.openlocfilehash: 7dfb0da5d6a2e0d9349f68ea97fb371641e0d506
ms.sourcegitcommit: 4c7f20dfd59fb5b5b1adfbbcbc9b7da07df5e479
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2020
ms.locfileid: "95324698"
---
# <a name="request-properties-and-clientrequestproperties"></a>要求屬性和 ClientRequestProperties

透過 .NET SDK 從 Kusto 提出要求時，請提供：

* 連接字串，表示要連接的服務端點、驗證參數，以及類似的連接相關資訊。 以程式設計方式，連接字串是透過 `KustoConnectionStringBuilder` 類別表示。

* 用來描述要求「範圍」之資料庫的名稱。

* 要求的文字 (查詢或命令) 本身。

* 用戶端提供給服務以及套用至要求的其他屬性。 以程式設計的方式，這些屬性是由稱為的類別所持有 `ClientRequestProperties` 。

##   <a name="clientrequestproperties"></a>ClientRequestProperties

用戶端要求屬性有許多用途。 
* 更輕鬆地進行調試。 例如，屬性可能會提供用來追蹤用戶端/服務互動的相互關聯字串。 
* 會影響套用至要求的限制和原則。 
* [查詢參數](../../query/queryparametersstatement.md) 可讓用戶端應用程式根據使用者輸入將 Kusto 查詢參數化。
[支援的屬性清單](#list-of-clientrequestproperties)。

`Kusto.Data.Common.ClientRequestProperties`類別包含三種資料。

* 命名屬性。
* 選項-選項名稱與選項值的對應。
* 參數-查詢參數名稱與查詢參數值的對應。

> [!NOTE]
> 有些命名屬性會標示為「請勿使用」。 用戶端不應指定這類屬性，也不會對服務造成任何影響。

## <a name="the-clientrequestid-x-ms-client-request-id-named-property"></a>ClientRequestId (x-----------------要求識別碼) 

此命名屬性具有要求的用戶端指定身分識別。 用戶端應該針對每個要求所傳送的要求指定唯一的每個要求值。 此值可讓您更輕鬆地執行偵錯工具，而且在某些情況下需要用到，例如查詢取消。

屬性的程式設計名稱是 `ClientRequestId` ，它會轉譯成 HTTP 標頭 `x-ms-client-request-id` 。

如果用戶端未指定值，則 SDK 會將這個屬性設定為 (隨機的) 值。

這個屬性的內容可以是任何可列印的唯一字串，例如 GUID。
不過，我們建議用戶端使用： *ApplicationName* `.` *ActivityName* `;` *UniqueId*

* *ApplicationName* 會識別提出要求的用戶端應用程式。
* *ActivityName* 會識別用戶端應用程式發出用戶端要求的活動類型。
* *UniqueId* 會識別特定的要求。

## <a name="the-application-x-ms-app-named-property"></a>應用程式 (x-ms-應用程式) 命名屬性

應用程式 (x ms 應用程式) 命名屬性具有提出要求之用戶端應用程式的名稱，並用於追蹤。

這個屬性的程式設計名稱是 `Application` ，它會轉譯成 HTTP 標頭 `x-ms-app` 。 您可以在 Kusto 連接字串中，將它指定為 `Application Name for Tracing` 。

如果用戶端未指定自己的值，此屬性將會設定為裝載 SDK 的進程名稱。

## <a name="the-user-x-ms-user-named-property"></a>使用者 (x-ms-使用者) 命名屬性

使用者 (x-ms-使用者) 命名屬性具有提出要求之使用者的身分識別，並用於追蹤。

這個屬性的程式設計名稱是 `User` ，它會轉譯成 HTTP 標頭 `x-ms-user` 。 您可以在 Kusto 連接字串中，將它指定為 `User Name for Tracing` 。

## <a name="controlling-request-properties-using-the-rest-api"></a>使用 REST API 控制要求屬性

發出 HTTP 要求給 Kusto 服務時，請使用 `properties` JSON 檔中的位置做為 POST 要求主體，以提供要求屬性。 

> [!NOTE]
> 某些屬性 (例如「用戶端要求識別碼」，這是用戶端提供給服務以識別要求) 的相互關聯識別碼，可以在 HTTP 標頭中提供，也可以在使用 HTTP GET 時設定。
如需詳細資訊，請參閱 [Kusto REST API request 物件](../rest/request.md)。

## <a name="providing-values-for-query-parameterization-as-request-properties"></a>提供查詢參數化作為要求屬性的值

Kusto 查詢可以在查詢文字中使用特製化的 [declare 查詢參數](../../query/queryparametersstatement.md) 語句來參考查詢參數。 此語句可讓用戶端應用程式以安全的方式，將 Kusto 查詢參數化，而不會擔心插入式攻擊。

以程式設計方式，使用 `ClearParameter` 、和方法來設定屬性值 `SetParameter` `HasParameter` 。

在 REST API 中，查詢參數會出現在與其他要求屬性相同的 JSON 編碼字串中。

## <a name="sample-client-code-for-using-request-properties"></a>使用要求屬性的範例用戶端程式代碼

```csharp
public static System.Data.IDataReader QueryKusto(
    Kusto.Data.Common.ICslQueryProvider queryProvider,
    string databaseName,
    string query)
{
    var queryParameters = new Dictionary<String, String>()
    {
        { "xIntValue", "111" },
        { "xStrValue", "abc" },
        { "xDoubleValue", "11.1" }
    };

    // Query parameters (and many other properties) are provided
    // by a ClientRequestProperties object handed alongside
    // the query:
    var clientRequestProperties = new Kusto.Data.Common.ClientRequestProperties(
        principalIdentity: null,
        options: null,
        parameters: queryParameters);

    // Having client code provide its own ClientRequestId is
    // highly recommended. It not only allows the caller to
    // cancel the query, but also makes it possible for the Kusto
    // team to investigate query failures end-to-end:
    clientRequestProperties.ClientRequestId
        = "MyApp.MyActivity;"
        + Guid.NewGuid().ToString();

    // This is an example for setting an option
    // ("notruncation", in this case). In most cases this is not
    // needed, but it's included here for completeness:
    clientRequestProperties.SetOption(
        Kusto.Data.Common.ClientRequestProperties.OptionNoTruncation,
        true);
 
    try
    {
        return queryProvider.ExecuteQuery(query, clientRequestProperties);
    }
    catch (Exception ex)
    {
        Console.WriteLine(
            "Failed invoking query '{0}' against Kusto."
            + " To have the Kusto team investigate this failure,"
            + " please open a ticket @ https://aka.ms/kustosupport,"
            + " and provide: ClientRequestId={1}",
            query, clientRequestProperties.ClientRequestId);
        return null;
    }
}
```

## <a name="list-of-clientrequestproperties"></a>ClientRequestProperties 清單

<!-- The following text can be re-produced by running: Kusto.Cli.exe -focus "#crp -doc" -->

* `deferpartialqueryfailures` (*OptionDeferPartialQueryFailures*) ：若為 true，則會停用報告部分查詢失敗作為結果集的一部分。 布林
* `materialized_view_shuffle` (*OptionMaterializedViewShuffleQuery*) ：針對查詢中所參考之具體化視圖使用隨機策略的提示。
屬性是具體化視圖名稱的陣列，以及要使用的隨機索引鍵。
範例： ' dynamic ( [{"Name"： "V1"、"Keys"： ["版 k1"、"K2"]}] ) ' (隨機 view V1 by 版 k1、K2) 或 ' dynamic ( [{"Name"： "V1"}] ) ' (所有按鍵) 隨機 view V1 [dynamic]
* `max_memory_consumption_per_query_per_node` (*OptionMaxMemoryConsumptionPerQueryPerNode*) ：覆寫整個查詢可針對每個節點配置的預設最大記憶體數量。 UInt64
* `maxmemoryconsumptionperiterator` (*OptionMaxMemoryConsumptionPerIterator*) ：覆寫查詢運算子可能配置的預設最大記憶體數量。 UInt64
* `maxoutputcolumns` (*OptionMaxOutputColumns*) ：覆寫允許查詢產生的預設最大資料行數目。 遠
* `norequesttimeout` (*OptionNoRequestTimeout*) ：可將要求超時設定為其最大值。 布林
* `notruncation` (*OptionNoTruncation*) ：允許隱藏傳回給呼叫端的查詢結果截斷。 布林
* `push_selection_through_aggregation` (*OptionPushSelectionThroughAggregation*) ：若為 true，則透過匯總推送簡單選取 [布林值]
* `query_bin_auto_at` (*QueryBinAutoAt*) ：評估 bin_auto ( # A3 函式時，所要使用的起始值。 [LiteralExpression]
* `query_bin_auto_size` (*QueryBinAutoSize*) ：評估 bin_auto ( # A3 函式時，要使用的 bin 大小值。 [LiteralExpression]
* `query_cursor_after_default` (*OptionQueryCursorAfterDefault*) ：當呼叫不含參數的情況下，cursor_after ( # A3 函式的預設參數值。 [string]
* `query_cursor_before_or_at_default` (*OptionQueryCursorBeforeOrAtDefault*) ：當呼叫不含參數的情況下，cursor_before_or_at ( # A3 函式的預設參數值。 [string]
* `query_cursor_current` (*OptionQueryCursorCurrent*) ：覆寫 cursor_current ( # A3 或 current_cursor ( # A5 函數所傳回的資料指標值。 [string]
* `query_cursor_disabled` (*OptionQueryCursorDisabled*) ：停用查詢內容中資料指標函數的使用方式。 布林
* `query_cursor_scoped_tables` (*OptionQueryCursorScopedTables*) ：應範圍設定為 cursor_after_default 的資料表名稱清單。 cursor_before_or_at_default (上限是選擇性的) 。 動態
* `query_datascope` (*OptionQueryDataScope*) ：控制查詢的 datascope--查詢是否適用于所有資料，或只套用至部分資料。 [' default '、' all ' 或 ' hotcache ']
* `query_datetimescope_column` (*OptionQueryDateTimeScopeColumn*) ：控制查詢的日期時間範圍 (query_datetimescope_to/query_datetimescope_from) 的資料行名稱。 字串
* `query_datetimescope_from` (*OptionQueryDateTimeScopeFrom*) ：控制查詢的日期時間範圍 (最早的) --只在 query_datetimescope_column 定義 (時，用來做為自動套用的篩選) 。 [DateTime]
* `query_datetimescope_to` (*OptionQueryDateTimeScopeTo*) ：控制查詢的日期時間範圍 (最新的) --僅在 query_datetimescope_column 定義 (時，用來做為自動套用的篩選) 。 [DateTime]
* `query_distribution_nodes_span` (*OptionQueryDistributionNodesSpanSize*) ：如果設定，會控制子查詢合併的運作方式：執行節點會在每個子節點群組的查詢階層中引進額外的層級。此選項會設定子群組的大小。 加法
* `query_fanout_nodes_percent` (*OptionQueryFanoutNodesPercent*) ：要扇出執行的節點百分比。 加法
* `query_fanout_threads_percent` (*OptionQueryFanoutThreadsPercent*) ：要扇出執行的執行緒百分比。 加法
* `query_force_row_level_security` (*OptionQueryForceRowLevelSecurity*) ：如果已指定，則會強制資料列層級安全性規則，即使已停用 row_level_security 原則 [布林值]
* `query_language` (*OptionQueryLanguage*) ：控制要如何解讀查詢文字。 [' csl '、' kql ' 或 ' sql ']
* `query_max_entities_in_union` (*OptionMaxEntitiesToUnion*) ：覆寫允許查詢產生的預設最大資料行數目。 遠
* `query_now` (*OptionQueryNow*) ：覆寫 now (0 s) 函數所傳回的日期時間值。 [DateTime]
* `query_python_debug` (*OptionDebugPython*) ：如果設定，請為列舉的 python 節點產生 python debug 查詢 (預設的第一個) 。 [布林值或 Int]
* `query_results_apply_getschema` (*OptionQueryResultsApplyGetSchema*) ：如果設定，則會抓取查詢結果中每個表格式資料的架構，而不是資料本身。 布林
* `query_results_cache_max_age` (*OptionQueryResultsCacheMaxAge*) ：如果是正數，則控制允許 Kusto 傳回的快取查詢結果的最長存留期 [TimeSpan]
* `query_results_progressive_row_count` (*OptionProgressiveQueryMinRowCountPerUpdate*) ：提示是否要在每個更新中傳送多少記錄， (只會在設定 OptionResultsProgressiveEnabled 時生效) 
* `query_results_progressive_update_period` (*OptionProgressiveProgressReportPeriod*) ： Kusto 的提示，以傳送進度畫面的頻率 (只會在設定 OptionResultsProgressiveEnabled 時生效) 
* `query_take_max_records` (*OptionTakeMaxRecords*) ：可將查詢結果限制為此筆記錄數目。 遠
* `queryconsistency` (*OptionQueryConsistency*) ：控制查詢一致性。 [' strongconsistency ' 或 ' normalconsistency ' 或 ' weakconsistency ']
* `request_block_row_level_security` (*OptionRequestBlockRowLevelSecurity*) ：如果有指定，會封鎖存取已啟用 row_level_security 原則的資料表 [布林值]
* `request_callout_disabled` (*OptionRequestCalloutDisabled*) ：若已指定，則表示要求無法呼叫使用者提供的服務。 布林
* `request_description` (*OptionRequestDescription*) ：要求的作者想要包含為要求描述的任意文字。 字串
* `request_external_table_disabled` (*OptionRequestExternalTableDisabled*) ：若已指定，則表示要求無法在 ExternalTable 中叫用程式碼。 布林
* `request_impersonation_disabled` (*OptionDoNotImpersonate*) ：若已指定，則表示服務不應該模擬呼叫者的身分識別。 布林
* `request_readonly` (*OptionRequestReadOnly*) ：若有指定，則表示要求必須無法撰寫任何內容。 布林
* `request_remote_entities_disabled` (*OptionRequestRemoteEntitiesDisabled*) ：若已指定，則表示要求無法存取遠端資料庫和叢集。 布林
* `request_sandboxed_execution_disabled` (*OptionRequestSandboxedExecutionDisabled*) ：如果已指定，則表示要求無法在沙箱中叫用程式碼。 布林
* `results_progressive_enabled` (*OptionResultsProgressiveEnabled*) ：如果設定，則會啟用漸進式查詢資料流程
* `servertimeout` (*OptionServerTimeout*) ：覆寫預設的要求超時。 TimeSpan
* `truncationmaxrecords` (*OptionTruncationMaxRecords*) ：覆寫允許查詢傳回給呼叫者的預設最大記錄數目 (截斷) 。 遠
* `truncationmaxsize` (*OptionTruncationMaxSize*) ：覆寫允許查詢傳回給呼叫者的預設資料大小上限 (截斷) 。 遠
* `validate_permissions` (*OptionValidatePermissions*) ：驗證使用者執行查詢的許可權，而且不會執行查詢本身。 布林
