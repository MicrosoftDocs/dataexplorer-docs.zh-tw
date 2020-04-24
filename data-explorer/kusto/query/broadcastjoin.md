---
title: 廣播聯接 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的廣播聯接。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 72c89bf2160f8f5b735fd8c93f9519feae9114d9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517301"
---
# <a name="broadcast-join"></a>廣播加入

今天,常規聯接在單個群集節點上執行。
廣播聯接是一種聯接的執行策略,它將在群集節點上分發它,當聯接的左側較小(最多 100K 記錄)時,它很有用,在這種情況下,聯接將比常規聯接更具性能。

如果聯接的左側是一個小資料集,則可以使用以下語法(hint.策略 = 廣播)在廣播模式下運行聯接:

```kusto
lookupTable 
| join hint.strategy = broadcast (factTable) on key
```

在聯接後其他運算子(如`summarize`) 例如,在此查詢中:

```kusto
lookupTable 
| join hint.strategy = broadcast (factTable) on Key
| summarize dcount(Messages) by Timestamp, Key
```