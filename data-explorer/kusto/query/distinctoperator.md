---
title: 不同的運算子 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的不同運算符。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4287ca48d3fb5006e67a9266ea05287a7d8a06f6
ms.sourcegitcommit: 29018b3db4ea7d015b1afa65d49ecf918cdff3d6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "82030358"
---
# <a name="distinct-operator"></a>distinct 運算子

生成具有輸入表提供的列的不同組合的表。 

```kusto
T | distinct Column1, Column2
```

生成具有輸入表中所有列的不同組合的表。

```kusto
T | distinct *
```

**範例**

顯示水果和價格的獨特組合。

```kusto
Table | distinct fruit, price
```

:::image type="content" source="images/distinctoperator/distinct.PNG" alt-text="不同":::

**注意事項**

* 與`summarize by ...`不同`distinct`,運算符支援提供星號 ()`*`作為組鍵,因此更易於用於寬表。
* 如果按鍵分組具有高基數,則使用`summarize by ...`[隨機操作策略](shufflequery.md)可能很有用。