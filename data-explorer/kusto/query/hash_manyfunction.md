---
title: 'hash_many ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 hash_many。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/06/2020
ms.openlocfilehash: a323491a2d3c4e78684c8bcaff6de8c55573d61a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243286"
---
# <a name="hash_many"></a>hash_many()

傳回多個值的組合雜湊值。

## <a name="syntax"></a>語法

`hash_many(`*s1* `,`*s2* [ `,` *s3* ...]`)`

## <a name="arguments"></a>引數

* *s1*、 *s2*、...、 *sN*：將會雜湊在一起的輸入值。

## <a name="returns"></a>傳回

給定純量的合併雜湊值。

## <a name="examples"></a>範例

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print value1 = "Hello", value2 = "World"
| extend combined = hash_many(value1, value2)
```

|value1|value2|聯合|
|---|---|---|
|您好|World|-1440138333540407281|
