---
title: hash_combine （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 hash_combine （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/19/2019
ms.openlocfilehash: f71a0d0cdfa4fe0ca8cdb84e65a271ee42bc7dc7
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347607"
---
# <a name="hash_combine"></a>hash_combine()

結合兩個或多個雜湊的雜湊值。

## <a name="syntax"></a>語法

`hash_combine(`*h1* `,`*h2* [ `,` *h3* ...]`)`

## <a name="arguments"></a>引數

* *h1*：代表第一個雜湊值的 Long 值。
* *h2*：代表第二個雜湊值的 Long 值。
* *hN*：代表 n 個雜湊值的 Long 值。

## <a name="returns"></a>傳回

給定純量的結合雜湊值。

## <a name="examples"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print value1 = "Hello", value2 = "World"
| extend h1 = hash(value1), h2=hash(value2)
| extend combined = hash_combine(h1, h2)
```

|value1|value2|h1|h2|混|
|---|---|---|---|---|
|您好|World|753694413698530628|1846988464401551951|-1440138333540407281|
