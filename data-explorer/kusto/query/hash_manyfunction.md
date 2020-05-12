---
title: hash_many （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 hash_many （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/06/2020
ms.openlocfilehash: 66ca1e5ff330a4b39ab769b0e3e8d6359eed9c00
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226664"
---
# <a name="hash_many"></a>hash_many()

傳回多個值的結合雜湊值。

**語法**

`hash_many(`*s1* `,`*s2* [ `,` *s3* ...]`)`

**引數**

* *s1*、 *s2*、...、 *sN*：將會雜湊在一起的輸入值。

**傳回**

給定純量的結合雜湊值。

**範例**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print value1 = "Hello", value2 = "World"
| extend combined = hash_many(value1, value2)
```

|value1|value2|混|
|---|---|---|
|您好|World|-1440138333540407281|
