---
title: 專案-重新命名運算子-Azure 資料總管
description: 本文說明 Azure 資料總管中的專案重新命名運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 68581cfe4b3828823ced7d4704eb08df5d2aefa7
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373164"
---
# <a name="project-rename-operator"></a>project-rename 運算子

重新命名結果輸出中的資料行。

```kusto
T | project-rename new_column_name = column_name
```

**語法**

*T* `| project-rename` *NewColumnName*  =  *ExistingColumnName* [ `,` ...]

**引數**

* *T*：輸入資料表。
* *NewColumnName：* 資料行的新名稱。 
* *ExistingColumnName：* 資料行的現有名稱。 

**傳回**

具有與現有資料表中相同順序之資料行的資料表，並重新命名資料行。


**範例**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print a='a', b='b', c='c'
|  project-rename new_b=b, new_a=a
```

|new_a|new_b|c|
|---|---|---|
|a|b|c|
