---
title: 具體化（）-Azure 資料總管
description: 本文說明 Azure 資料總管中的具體化（）函數。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/06/2020
ms.openlocfilehash: f5ea896d4701aa5aec1b22c1ff20853aea18f065
ms.sourcegitcommit: be1bbd62040ef83c08e800215443ffee21cb4219
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664937"
---
# <a name="materialize"></a>materialize()

允許在查詢執行期間以其他子查詢可以參考部分結果的方式，來快取子查詢結果。
 
**語法**

`materialize(`*expression*`)`

**引數**

* *expression*：要在查詢執行期間評估和快取的表格式運算式。

**提示**

* 當其運算元具有可執行一次的相互子查詢時，請使用具體化搭配 join 或 union。 請參閱以下範例。

* 在需要聯結/聯集分岔流程的情況下也很有用。

* 當您提供快取的結果名稱時，具體化只能在 let 語句中使用。

**注意**

* 具體化的快取大小限制為**5 GB**。 
  這是每個叢集節點的限制，而且會與同時執行的所有查詢相互同步。
  如果查詢使用 `materialize()` ，而且快取無法保存任何其他資料，則查詢將會中止並產生錯誤。

**範例**

下列範例顯示如何 `materialize()` 使用來改善查詢的效能。
運算式 `_detailed_data` 是使用函數所定義 `materialize()` ，因此只會計算一次。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let _detailed_data = materialize(StormEvents | summarize Events=count() by State, EventType);
_detailed_data
| summarize TotalStateEvents=sum(Events) by State
| join (_detailed_data) on State
| extend EventPercentage = Events*100.0 / TotalStateEvents
| project State, EventType, EventPercentage, Events
| top 10 by EventPercentage
```

|State|EventType|EventPercentage|事件|
|---|---|---|---|
|夏威夷 WATERS|Waterspout|100|2|
|LAKE 安大略|航海 Thunderstorm 風|100|8|
|阿拉斯加|Waterspout|100|4|
|大西洋北部|航海 Thunderstorm 風|95.2127659574468|179|
|LAKE ERIE F.。。|航海 Thunderstorm 風|92.5925925925926|25|
|E 太平洋|Waterspout|90|9|
|LAKE 密歇根|航海 Thunderstorm 風|85.1648351648352|155|
|LAKE HURON|航海 Thunderstorm 風|79.3650793650794|50|
|墨西哥|航海 Thunderstorm 風|71.7504332755633|414|
|夏威夷|高度衝浪|70.0218818380744|320|


下列範例會產生一組亂數字，並計算： 
* 集合中的相異值數目（Dcount）
* 集合中的前三個值 
* 集合中所有這些值的總和 
 
這種作業可以使用[批次](batches.md)和具體化來完成：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let randomSet = 
    materialize(
        range x from 1 to 3000000 step 1
        | project value = rand(10000000));
randomSet | summarize Dcount=dcount(value);
randomSet | top 3 by value;
randomSet | summarize Sum=sum(value)
```

結果集1：  

|Dcount|
|---|
|2578351|

結果集2： 

|value|
|---|
|9999998|
|9999998|
|9999997|

結果集3： 

|Sum|
|---|
|15002960543563|
