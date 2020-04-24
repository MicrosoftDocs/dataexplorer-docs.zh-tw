---
title: 前() - Azure 資料資源管理員 |微軟文件
description: 本文在 Azure 數據資源管理器中介紹了前一個。"
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7d155f1a1cd113f73acc779e6c2e73d5f537e71c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519086"
---
# <a name="ago"></a>ago()

從目前的 UTC 時鐘時間減去指定的時間範圍。

```kusto
ago(1h)
ago(1d)
```

和 `now()`一樣，此函式可在陳述式中多次使用，而且所參考的 UTC 時鐘時間在所有具現化中皆相同。

**語法**

`ago(`*a_timespan*`)`

**引數**

* a_timespan︰** 要從目前的 UTC 時鐘時間 (`now()`) 減去的間隔。

**傳回**

`now() - a_timespan`

**範例**

時間戳記為過去一小時的所有資料列︰

```kusto
T | where Timestamp > ago(1h)
```