---
title: 廣播加入-Azure 資料總管
description: 本文說明 Azure 資料總管中的廣播聯結。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0cbcd71cc4582a4f5dd966001ae32fd4cedf0c1e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245283"
---
# <a name="broadcast-join"></a>廣播聯結

現今的一般聯結是在單一叢集節點上執行。
「廣播聯結」是聯結的執行策略，它會透過叢集節點散發。 當聯結的左邊是小型 (最多 100 K 筆記錄) 時，此策略會很有用。 在此情況下，廣播聯結的效能會比一般聯結更高。

如果聯結的左邊是小型資料集，則您可以使用下列語法 (提示，在廣播模式中執行聯結。策略 = 廣播) ：

```kusto
lookupTable 
| join hint.strategy = broadcast (factTable) on key
```

在聯結後面接著其他運算子（例如）的情況下，效能改進將更加顯著 `summarize` 。 例如，在此查詢中：

```kusto
lookupTable 
| join hint.strategy = broadcast (factTable) on Key
| summarize dcount(Messages) by Timestamp, Key
```