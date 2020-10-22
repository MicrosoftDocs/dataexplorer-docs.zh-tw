---
title: 專案-重新命名運算子-Azure 資料總管
description: 本文說明 Azure 資料總管中的專案重新命名運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9c46c8d0271516c56c3549212ad7b74f721ce81b
ms.sourcegitcommit: ee904f45e3eb3feab046263aa9956cb7780a056d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92356498"
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

資料表，其資料行的順序與現有資料表中的資料行相同，而且資料行已重新命名。

## <a name="examples"></a>範例

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print a='a', b='b', c='c'
|  project-rename new_b=b, new_a=a
```

|new_a|new_b|c|
|---|---|---|
|a|b|c|
