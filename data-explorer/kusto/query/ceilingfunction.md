---
title: 天花板() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的天花板()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f2ecd043c43bb1af6530364d200d5dc9db640f95
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517250"
---
# <a name="ceiling"></a>ceiling()

計算大於或等於指定數值表達式的最小整數。

**語法**

`ceiling(`*X.*`)`

**引數**

* *x*: 實數。

**傳回**

* 大於或等於指定數值表達式的最小整數。 

**範例**

```kusto
print c1 = ceiling(-1.1), c2 = ceiling(0), c3 = ceiling(0.9)
```

|c1|c2|c3|
|---|---|---|
|-1|0|1|