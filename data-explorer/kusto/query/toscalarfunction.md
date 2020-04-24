---
title: 到斯卡拉爾() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 toscalar()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 60fe8123760a9921bfa7abfacbbdffba6dba8d7b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505894"
---
# <a name="toscalar"></a>toscalar()

返回計算表達式的標量常量值。 

此函數對於需要暫存計算的查詢很有用,例如計算事件總數,然後將其用於篩選超過所有事件特定百分比的組。 

**語法**

`toscalar(`*表達*`)`

**引數**

* *表示式*: 將計算用於標量轉換的運算式  

**傳回**

計算表達式的標量常量值。
如果表達式結果是表格,則第一列和第一行將進行轉換。

> [!TIP]
> 使用`toscalar()`時,可以使用[let 語句](letstatement.md)來讀取查詢的可讀性。

**注意事項**

`toscalar()`可以在查詢執行期間計算恆定次數。
換句話說,`toscalar()`函數不能應用於行級別(對於每行方案)。

**範例**

`Start`以下查詢計算`End`,並`Step`作為 標量常量,並將`range`其用於計算。 

```kusto
let Start = toscalar(print x=1);
let End = toscalar(range x from 1 to 9 step 1 | count);
let Step = toscalar(2);
range z from Start to End step Step | extend start=Start, end=End, step=Step
```

|z|start|end|步驟|
|---|---|---|---|
|1|1|9|2|
|3|1|9|2|
|5|1|9|2|
|7|1|9|2|
|9|1|9|2|