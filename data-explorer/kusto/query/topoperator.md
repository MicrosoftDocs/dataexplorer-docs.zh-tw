---
title: 頂部運算符 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的頂級運算符。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bb1d24dd0cd1dfeecb1925e474eb77e8cb86121f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505911"
---
# <a name="top-operator"></a>top 運算子

傳回按指定資料行排序的前 N ** 個記錄。

```kusto
T | top 5 by Name desc nulls last
```

**語法**

*T* `| top`*行數*`by`*運算式*`asc` | `desc``nulls first` | [`nulls last`] |

**引數**

* *行數*:要返回的*T*行數。 您可以指定任何數字運算式。
* *運算式*:要排序的標量運算式。 值的類型必須是數值、日期、時間或字串。
* `asc` 或 `desc` (預設值) 可能會出現，以控制實際上是從範圍的「下限」或「上限」進行選取。
* `nulls first`(訂單的`asc`預設值)`nulls last`或 (`desc`訂單的 默認值)可能顯示控制空值是位於範圍的開頭還是結尾。


**技巧**

* `top 5 by name`從語義和性能角度等效`sort by name | take 5`於表達式。
* 使用[頂級嵌套](topnestedoperator.md)運算符生成分層(嵌套)頂級結果。