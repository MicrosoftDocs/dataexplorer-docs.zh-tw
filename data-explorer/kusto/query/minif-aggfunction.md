---
title: minif() (聚合函數) - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 minif()(聚合函數)。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0aad254ec01e83bdb07734e5b309c1450512b446
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512354"
---
# <a name="minif-aggregation-function"></a>最小值 () (聚合函數)

返回 *"謂詞"*`true`計算到 的組中的最小值。

* 只能在[匯總](summarizeoperator.md)的聚合上下文中使用

另請參閱 - [min()函數](min-aggfunction.md),該函數返回整個組中的最小值,而不帶謂詞表達式。

**語法**

`summarize``minif(` *Expr*`,`*謂詞*`)`

**引數**

* *Expr*:將用於聚合計算的運算式。
* *謂詞*: 謂詞,如果為 true,則*Expr*計算值將檢查為最小值。

**傳回**

*謂詞*計算`true`到 的組中*Expr*的最小值。

**範例**

```kusto
range x from 1 to 100 step 1
| summarize minif(x, x > 50)
```

|minif_x|
|---|
|51|