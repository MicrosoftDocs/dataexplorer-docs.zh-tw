---
title: Alter 具體化 view-Azure 資料總管
description: 本文說明如何在 Azure 資料總管中改變具體化的觀點。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/30/2020
ms.openlocfilehash: ac9fb575f46bb60e313da4fa2b3c023ac826daec
ms.sourcegitcommit: 21dee76964bf284ad7c2505a7b0b6896bca182cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2020
ms.locfileid: "91057118"
---
# <a name="alter-materialized-view"></a>。 alter 具體化-view

改變 [具體化視圖](materialized-view-overview.md) 可以用來變更具體化視圖的查詢，同時保留視圖中的現有資料。

改變具體化視圖需要 [資料庫管理員](../access-control/role-based-authorization.md) 許可權，或具體化視圖的系統管理員許可權。 如需詳細資訊，請參閱 [安全性角色管理](../security-roles.md)。

> [!WARNING]
> 改變具體化視圖時，請特別小心。 不正確的使用可能會導致資料遺失。

## <a name="syntax"></a>語法

`.alter` `materialized-view`  
[ `with` `(` *PropertyName* `=` *PropertyValue* `,` ... `)` ]  
*ViewName* `on table`*SourceTableName*  
`{`  
    &nbsp;&nbsp;&nbsp;&nbsp;*查詢*  
`}`

## <a name="arguments"></a>引數

|引數|類型|描述
|----------------|-------|---|
|ViewName|String|具體化視圖名稱。|
|SourceTableName|String|定義視圖的來源資料表名稱。|
|查詢|String|具體化 view 查詢。|

## <a name="properties"></a>屬性

`dimensionTables`是具體化-view alter 命令中唯一支援的屬性。 當查詢參考維度資料表時，應使用此屬性。 如需詳細資訊，請參閱 [。建立具體化-view](materialized-view-create.md) 命令。

## <a name="use-cases"></a>使用案例

* 將 `avg` `T | summarize count(), min(Value) by Id` view 查詢修改為，以將匯總加入至視圖-例如，將匯總加入至 `T | summarize count(), min(Value), avg(Value) by Id` 。
* 變更摘要運算子以外的運算子。 Tor 範例中，藉由變更來篩選掉一些記錄  `T | summarize arg_max(Timestamp, *) by User` `T | where User != 'someone' | summarize arg_max(Timestamp, *) by User` 。
* 因為來源資料表中的變更，而不變更查詢的 Alter。 Tor 範例假設的視圖 `T | summarize arg_max(Timestamp, *) by Id` 未設定為 `autoUpdateSchema` (請參閱) 。 [建立具體化視圖](materialized-view-create.md) 命令。 如果在視圖的來源資料表中加入或移除資料行，就會自動停用此視圖。 使用完全相同的查詢來執行 alter 命令，以變更具體化視圖的架構，使其符合新的資料表架構。 您仍然必須在變更之後，使用 [ [啟用具體化 view](materialized-view-enable-disable.md) ] 命令來明確啟用此視圖。

## <a name="alter-materialized-view-limitations"></a>Alter 具體化 view 限制

* **不支援的變更：**
    * 不支援變更資料行類型。
    * 不支援重新命名資料行。 例如，將的視圖變更 `T | summarize count() by Id` 為會卸載資料 `T | summarize Count=count() by Id` 行， `count_` 並建立新的資料行，此資料行一 `Count` 開始只會包含 null。
    * 不支援對具體化 view group by 運算式所做的變更。

* **對現有資料的影響：**
    * 改變具體化視圖並不會影響現有的資料。
    * 新的資料行會接收所有現有記錄的 null，直到記錄內嵌 post alter 命令修改 null 值為止。
        * 例如：如果的視圖 `T | summarize count() by bin(Timestamp, 1d)` 改變為 `T | summarize count(), sum(Value) by bin(Timestamp, 1d)` ，則針對在 `Timestamp=T` 變更視圖之前已經處理過的記錄， `sum` 資料行將包含部分資料。 此視圖只包含在改變執行後所處理的記錄。
    * 將篩選準則新增至查詢並不會變更已經具體化的記錄。 篩選只會套用至新內嵌的記錄。
