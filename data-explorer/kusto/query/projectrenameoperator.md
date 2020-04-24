---
title: 項目重新命名運算子 ─ Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的專案重命名運算符。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1f7fbf2efbfd3ed1dfac9129ec21f5a13c51481c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510858"
---
# <a name="project-rename-operator"></a>project-rename 運算子

重新命名結果輸出中的欄位。

```kusto
T | project-rename new_column_name = column_name
```

**語法**

*T* `| project-rename` *新欄位* = *名稱( 現有欄位 )*[`,` ... 】

**引數**

* *T*: 輸入表。
* *新欄位:* 列的新名稱。 
* *現有欄位:* 列的現有名稱。 

**傳回**

具有與現有表中相同的列順序的表,並重新命名列。


**範例**

```kusto
print a='a', b='b', c='c'
|  project-rename new_b=b, new_a=a
```

|new_a|new_b|c|
|---|---|---|
|a|b|c|