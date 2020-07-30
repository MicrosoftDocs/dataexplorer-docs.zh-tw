---
title: stdev （）（彙總函式）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 stdev （）（彙總函式）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 18722a544ea3bbd0e19922d1d8988a3604b4d200
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87342898"
---
# <a name="stdev-aggregation-function"></a>stdev （）（彙總函式）

在群組中計算*Expr*的標準差，並考慮將群組當做[範例](https://en.wikipedia.org/wiki/Sample_%28statistics%29)。 

* 使用的公式：

:::image type="content" source="images/stdev-aggfunction/stdev-sample.png" alt-text="Stdev 範例":::

* 只能在[匯總](summarizeoperator.md)的內容中使用

## <a name="syntax"></a>語法

總結 `stdev(` *Expr*`)`

## <a name="arguments"></a>引數

* *Expr*：將用於匯總計算的運算式。 

## <a name="returns"></a>傳回

整個群組的*Expr*標準差值。
 
## <a name="examples"></a>範例

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), stdev(x)

```

|list_x|stdev_x|
|---|---|
|[1，2，3，4，5]|1.58113883008419|