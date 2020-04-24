---
title: hash_many() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的hash_many()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/06/2020
ms.openlocfilehash: e98f9d1d956d15cd7a61e7873f9b1dd34c6ae288
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514173"
---
# <a name="hash_many"></a>hash_many()

返回多個值的組合哈希值。

**語法**

`hash_many(`*s1* `,` *s2* =`,` *s3* ...]`)`

**引數**

* *s1,* *s2*, *..., sN*: 將一起哈希的輸入值。

**傳回**

給定標量的組合哈希值。

**範例**

```kusto
print value1 = "Hello", value2 = "World"
| extend combined = hash_many(value1, value2)
```

|value1|value2|聯合|
|---|---|---|
|您好|World|-1440138333540407281|