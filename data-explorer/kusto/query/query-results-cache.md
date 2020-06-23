---
title: 查詢結果快取-Azure 資料總管
description: 本文說明 Azure 資料總管中的查詢結果快取功能。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: amitof
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 7373af48ef581e4b64f1cfc34ece0c16a416fc08
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128898"
---
# <a name="query-results-cache"></a>查詢結果快取

Kusto 包含查詢結果快取。 您可以選擇在發出查詢時取得快取的結果。 如果快取可以傳回查詢的結果，您將會遇到更佳的查詢效能和較低的資源耗用量。 不過，此效能的代價是在結果中有一些「過期」。

## <a name="use-the-cache"></a>使用快取

將 `query_results_cache_max_age` 選項設定為查詢的一部分，以使用查詢結果快取。 您可以在 [查詢文字] 或 [用戶端要求] 屬性中設定此選項。 例如：

```kusto
set query_results_cache_max_age = time(5m);
GithubEvent
| where CreatedAt > ago(180d)
| summarize arg_max(CreatedAt, Type) by Id
```

選項值是 `timespan` ，表示結果快取的最大「存留期」（以查詢開始時間為測量單位）。 在設定的時間範圍外，快取專案已過時，且不會再次使用。 將值設定為0相當於未設定選項。

## <a name="compatibility-between-queries"></a>查詢之間的相容性

### <a name="identical-queries"></a>相同的查詢

Query_results_cache 只會針對視為「完全相同」的查詢，傳回先前快取查詢的結果。 如果符合下列所有條件，則會將兩個查詢視為相同：

* 這兩個查詢具有相同的標記法（如 UTF-8 字串）。
* 這兩個查詢會對相同的資料庫進行。
* 這兩個查詢會共用相同的[用戶端要求屬性](../api/netfx/request-properties.md)。 基於快取目的，會忽略下列屬性：
   * [ClientRequestId](../api/netfx/request-properties.md#the-clientrequestid-x-ms-client-request-id-named-property)
   * [應用程式](../api/netfx/request-properties.md#the-application-x-ms-app-named-property)
   * [使用者](../api/netfx/request-properties.md#the-user-x-ms-user-named-property)

### <a name="incompatible-queries"></a>不相容的查詢

如果下列任一條件成立，則不會快取查詢結果：
 
* 查詢會參考已啟用[RestrictedViewAccess](../management/restrictedviewaccesspolicy.md)原則的資料表。
* 查詢會參考已啟用[RowLevelSecurity](../management/rowlevelsecuritypolicy.md)原則的資料表。
* 查詢會使用下列任何函數：
    * [current_principal](current-principalfunction.md)
    * [current_principal_details](current-principal-detailsfunction.md)
    * [current_principal_is_member_of](current-principal-ismemberoffunction.md)
* 此查詢是[跨叢集查詢](cross-cluster-or-database-queries.md)。
* 查詢會存取[外部資料表](schema-entities/externaltables.md)或[外部資料](externaldata-operator.md)。
* 查詢會使用「[評估外掛程式](evaluateoperator.md)」運算子。

## <a name="no-valid-cache-entry"></a>沒有有效的快取專案

如果找不到符合時間條件約束的快取結果，或快取中的「相同」查詢沒有快取的結果，則會執行查詢並快取其結果，但前提是： 

* 查詢執行成功完成，而且
* 查詢結果大小不會超過 16 MB。

## <a name="results-from-the-cache"></a>快取的結果

服務如何指出查詢結果是由快取提供服務？
回應查詢時，Kusto 會傳送額外的[ExtendedProperties](../api/rest/response.md)回應表，其中包含資料 `Key` 行和資料 `Value` 行。
快取的查詢結果將會在該資料表附加額外的資料列：
* 資料列的資料行 `Key` 將包含字串`ServerCache`
* 資料列的資料行 `Value` 將包含具有兩個欄位的屬性包：
   * `OriginalClientRequestId`-指定原始要求的[ClientRequestId](../api/netfx/request-properties.md#the-clientrequestid-x-ms-client-request-id-named-property)。
   * `OriginalStartedOn`-指定原始要求的執行開始時間。

## <a name="distribution"></a>散發

叢集節點不會共用快取。 每個節點在其「自己的私用儲存體」中都有專用的快取。 如果兩個相同的查詢落在不同的節點上，則會在這兩個節點上執行並快取查詢。 如果使用[弱式一致性](../concepts/queryconsistency.md)，可能會發生此程式。

## <a name="management"></a>管理性

支援下列管理和可檢視性命令：

* [顯示](../management/show-query-results-cache-command.md)快取。
* [清除](../management/clear-query-results-cache-command.md)快取。

## <a name="capacity"></a>Capacity

快取容量目前固定為每個叢集節點 1 GB。
收回原則是 LRU。
