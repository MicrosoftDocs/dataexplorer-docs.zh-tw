---
title: row_window_session() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 row_window_session()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 87e89443bc85125e705bc180ea0cdb9e599c9c13
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510246"
---
# <a name="row_window_session"></a>row_window_session()

`row_window_session()`計算[序列化列集中](./windowsfunctions.md#serialized-row-set)列的工作階段起始值。

**語法**

`row_window_session``(``,`從第一個最大距離在鄰居之間 [ 重新啟動 】 * * `,` * * * * `,` * *`)`

* *Expr*是一個表達式,其值在會話中分組在一起。
  空值生成空值,下一個值將啟動一個新會話。
  *Expr*必須是類型`datetime`的標量運算式。

* *Max離距離FromFirst*為開始新會話建立了一個標準 *:Expr*的當前值與會話開始時*Expr*值之間的最大距離。
  它是類型的`timespan`標量常數。

* *Max離距離鄰域*為啟動新會話建立了第二個標準:從*Expr*的一個值到下一個值的最大距離。
  它是類型的`timespan`標量常數。

* *重新啟動*是類型`boolean`的 可選標量運算式。 如果指定,則計算到`true`的每個值將立即重新啟動作業階段。

**傳回**

函數返回每個會話開頭的值。

**注意事項**

該函數具有以下概念計算模型:

1. 依順序遍過值*Expr*的輸入序列。

2. 對於每個值,確定它是否建立了新會話。

3. 如果它建立了一個新會話,則發出*Expr*的值。 否則,將發出*Expr*的前一個值 。

條件確定值表示新工作階段是否為以下邏輯 OR:

* 如果沒有上一個會話值,或者上一個會話值為空。

* 如果*Expr*的值等於或超過上一個工作階段值加上*MaxDistance FromFirst*。

* 如果*Expr*的值等於或超過*Expr*加上*最大距離鄰域*的上一個值。

**範例**

下面的範例展示如何計算具有兩列的表的工作階段開始值、標識序列的`ID`列以及提供每個記錄發生的時間`Timestamp`的列。 在此示例中,會話不能超過 1 小時,只要記錄相距不到 5 分鐘,會話就繼續。

```kusto
datatable (ID:string, Timestamp:datetime) [
    // ...
]
| sort by ID asc, Timestamp asc
| extend SessionStarted = row_window_session(Timestamp, 1h, 5m, ID != prev(ID))
```