---
title: hash_combine() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 hash_combine()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/19/2019
ms.openlocfilehash: d0c6375436df298a97c03ec2f06f7cd492d59d7f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514258"
---
# <a name="hash_combine"></a>hash_combine()

合併兩個或多個哈希值。

**語法**

`hash_combine(`*h1* `,` *h2* =`,` *h3* ...]`)`

**引數**

* *h1*: 表示第一個哈希值的長值。
* *h2*: 表示第二個哈希值的長值。
* *hN*: 表示 Nth 哈希值的長值。

**傳回**

給定標量的組合哈希值。

**範例**

```kusto
print value1 = "Hello", value2 = "World"
| extend h1 = hash(value1), h2=hash(value2)
| extend combined = hash_combine(h1, h2)
```

|value1|value2|h1|h2|聯合|
|---|---|---|---|---|
|您好|World|753694413698530628|1846988464401551951|-1440138333540407281|