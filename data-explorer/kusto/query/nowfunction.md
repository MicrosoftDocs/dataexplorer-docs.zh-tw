---
title: 現在() - Azure 資料資源管理員 |微軟文件
description: 本文現在介紹 Azure 數據資源管理器中的()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c1a130cfbd45c35ff1ba26ed6c47986fc186c89c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512048"
---
# <a name="now"></a>now()

返回當前 UTC 時鐘時間,可選地偏移給定的時間跨度。
此函式可在陳述式中多次使用，而且所參考的時鐘時間在所有執行個體皆相同。

```kusto
now()
now(-2d)
```

**語法**

`now(`(*位移*]`)`

**引數**

* *偏移*`timespan`量 :添加到當前 UTC 時鐘時間。 預設值︰0。

**傳回**

目前的 UTC 時鐘時間，格式為 `datetime`。

`now()` + *抵消* 

**範例**

決定從述詞所識別的事件算起的間隔︰

```kusto
T | where ... | extend Elapsed=now() - Timestamp
```