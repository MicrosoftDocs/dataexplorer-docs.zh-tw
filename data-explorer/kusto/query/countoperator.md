---
title: count 運算子-Azure 資料總管
description: 本文說明 Azure 資料總管中的 count 運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/16/2020
ms.openlocfilehash: 9a34734ebfee94646b2b2f15730f14f9d2709c6d
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227499"
---
# <a name="count-operator"></a>count 運算子

傳回輸入記錄集中的記錄數目。

**語法**

`T | count`

**引數**

*T*︰要計算記錄數目的表格式資料。

**傳回**

此函式會傳回含有單一記錄且資料行類型為 `long`的資料表。 唯一資料格的值是 T ** 中的記錄數目。 

**範例**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents | count
```
