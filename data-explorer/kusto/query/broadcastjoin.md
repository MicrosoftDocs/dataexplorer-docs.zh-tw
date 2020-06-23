---
title: 廣播聯結-Azure 資料總管
description: 本文說明 Azure 資料總管中的廣播聯結。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7987f44437d673b90954b674e63b70ac35e2432c
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128677"
---
# <a name="broadcast-join"></a>廣播聯結

今天，一般聯結會在單一叢集節點上執行。
「廣播聯結」是聯結的執行策略，它會將它散發在叢集節點上。 當聯結的左邊很小（最多 100 K 筆記錄）時，此策略會很有用。 在此情況下，廣播聯結的效能會比一般聯結更有效率。

如果聯結的左邊是小型資料集，則您可以使用下列語法（提示. 策略 = 廣播）在廣播模式下執行 join：

```kusto
lookupTable 
| join hint.strategy = broadcast (factTable) on key
```

在聯結後面接著其他運算子（例如）的情況下，效能改進會更明顯 `summarize` 。 例如，在此查詢中：

```kusto
lookupTable 
| join hint.strategy = broadcast (factTable) on Key
| summarize dcount(Messages) by Timestamp, Key
```