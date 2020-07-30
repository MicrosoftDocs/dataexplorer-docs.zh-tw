---
title: distinct 運算子-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的相異運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 233d3fdb0e25720b860a0c11515daec7c597dadd
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348338"
---
# <a name="distinct-operator"></a>distinct 運算子

產生資料表，其中包含輸入資料表之所提供資料行的相異組合。 

```kusto
T | distinct Column1, Column2
```

產生一個資料表，其中包含輸入資料表中所有資料行的相異組合。

```kusto
T | distinct *
```

## <a name="example"></a>範例

顯示水果和價格的相異組合。

```kusto
Table | distinct fruit, price
```

:::image type="content" source="images/distinctoperator/distinct.PNG" alt-text="Distinct":::

**備註**

* 不同于 `summarize by ...` ， `distinct` 運算子支援提供星號（ `*` ）做為群組索引鍵，讓寬型資料表更容易使用。
* 如果 group by 索引鍵是高基數，使用 `summarize by ...` 與[隨機播放策略](shufflequery.md)可能會很有用。