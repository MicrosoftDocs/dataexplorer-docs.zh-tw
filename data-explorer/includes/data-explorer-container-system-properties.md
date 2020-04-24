---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 02/27/2020
ms.author: orspodek
ms.openlocfilehash: a2297301a0b9c0540c73c0f50483cccfc3181a0f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81498821"
---
### <a name="event-system-properties-mapping"></a>事件系統屬性對應

> [!Note]
> * 單記錄事件支援系統屬性。
> * 對於`csv`映射,屬性在記錄的開頭添加。 對於`json`映射,將根據下拉清單中顯示的名稱添加屬性。

如果在表的 **「資料源」** 部分中選擇了**事件系統屬性**,則必須在表架構和映射中包括以下屬性。

**表架構範例**

如果資料包含三`Timespan`欄`Metric`(、`Value`與 ) 以及`x-opt-enqueued-time``x-opt-offset`包含的屬性是與, 請使用此指令建立或變更表架構:

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:string)
```

**CSV 對應範例**

運行以下命令將數據添加到記錄的開頭。 注意單位值。

```kusto
    .create table TestTable ingestion csv mapping "CsvMapping1"
    '['
    '   { "column" : "Timespan", "Properties":{"Ordinal":"2"}},'
    '   { "column" : "Metric", "Properties":{"Ordinal":"3"}},'
    '   { "column" : "Value", "Properties":{"Ordinal":"4"}},'
    '   { "column" : "EventHubEnqueuedTime", "Properties":{"Ordinal":"0"}},'
    '   { "column" : "EventHubOffset", "Properties":{"Ordinal":"1"}}'
    ']'
```
 
**JSON 對應範例**

數據是透過使用系統屬性名稱添加的,因為它們顯示在 **「資料連接**邊欄選項卡**事件」系統屬性**清單中。 執行以下命令：

```kusto
    .create table TestTable ingestion json mapping "JsonMapping1"
    '['
    '    { "column" : "Timespan", "Properties":{"Path":"$.timestamp"}},'
    '    { "column" : "Metric", "Properties":{"Path":"$.metric"}},'
    '    { "column" : "Value", "Properties":{"Path":"$.metric_value"}},'
    '    { "column" : "EventHubEnqueuedTime", "Properties":{"Path":"$.x-opt-enqueued-time"}},'
    '    { "column" : "EventHubOffset", "Properties":{"Path":"$.x-opt-offset"}}'
    ']'
```
