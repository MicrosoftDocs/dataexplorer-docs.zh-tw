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
ms.openlocfilehash: 9bc000ffa57d906c3e65e54e9daac5431f8dc276
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346009"
---
# <a name="project-rename-operator"></a>project-rename 運算子

重新命名結果輸出中的資料行。

```kusto
T | project-rename new_column_name = column_name
```

## <a name="syntax"></a>語法

*T* `| project-rename` *NewColumnName*  =  *ExistingColumnName* [ `,` ...]

## <a name="arguments"></a>引數

* *T*：輸入資料表。
* *NewColumnName：* 資料行的新名稱。 
* *ExistingColumnName：* 資料行的現有名稱。 

## <a name="returns"></a>傳回

具有與現有資料表中相同順序之資料行的資料表，並重新命名資料行。


## <a name="examples"></a>範例

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print a='a', b='b', c='c'
|  project-rename new_b=b, new_a=a
```

|new_a|new_b|c|
|---|---|---|
|a|b|c|
