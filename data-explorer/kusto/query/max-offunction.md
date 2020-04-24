---
title: max_of() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的max_of()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 68188ccd5eb814a22be166b8847d80193172813f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512490"
---
# <a name="max_of"></a>max_of()

返回多個計算的數字運算式的最大值。

```kusto
max_of(10, 1, -3, 17) == 17
```

**語法**

`max_of``(` *expr_1* *expr_2* expr_1expr_2... `,``)`

**引數**

* *expr_i*: 要計算的標量運算式。

- 所有參數必須具有相同的類型。
- 最多支援 64 個參數。

**傳回**

所有參數運算式的最大值。

**範例**

```kusto
print result = max_of(10, 1, -3, 17) 
```

|result|
|---|
|17|