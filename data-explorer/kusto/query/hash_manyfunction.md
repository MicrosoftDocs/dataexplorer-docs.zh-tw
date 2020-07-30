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
ms.openlocfilehash: 3d0f389264d078d2b55ac06214bb3b820fcf7f13
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347590"
---
# <a name="hash_many"></a>hash_many()

傳回多個值的結合雜湊值。

## <a name="syntax"></a>語法

`hash_many(`*s1* `,`*s2* [ `,` *s3* ...]`)`

## <a name="arguments"></a>引數

* *s1*、 *s2*、...、 *sN*：將會雜湊在一起的輸入值。

## <a name="returns"></a>傳回

給定純量的結合雜湊值。

## <a name="examples"></a>範例

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print value1 = "Hello", value2 = "World"
| extend combined = hash_many(value1, value2)
```

|value1|value2|混|
|---|---|---|
|您好|World|-1440138333540407281|
