---
title: 符號() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的符號()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 86a57ed2fb7d43daf300731fe48233eca318bded
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507560"
---
# <a name="sign"></a>sign()

數位運算式符號

**語法**

`sign(`*X.*`)`

**引數**

* *x*: 實數。

**傳回**

* 指定表達式的正 (+1)、零 (0) 或負 (-1) 符號。 

**範例**

```kusto
print s1 = sign(-42), s2 = sign(0), s3 = sign(11.2)

```

|s1|s2|s3|
|---|---|---|
|-1|0|1|