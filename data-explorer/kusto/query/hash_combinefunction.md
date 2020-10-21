---
title: 'hash_combine ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 hash_combine。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/19/2019
ms.openlocfilehash: 91a178ded007ff75d96ed73f864276aa7d193d8c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92244770"
---
# <a name="hash_combine"></a>hash_combine()

結合兩個或多個雜湊的雜湊值。

## <a name="syntax"></a>語法

`hash_combine(`*h1* `,`*h2* [ `,` *h3* ...]`)`

## <a name="arguments"></a>引數

* *h1*：代表第一個雜湊值的 Long 值。
* *h2*：代表第二個雜湊值的 Long 值。
* *hN*：代表第 n 個雜湊值的 Long 值。

## <a name="returns"></a>傳回

給定純量的合併雜湊值。

## <a name="examples"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print value1 = "Hello", value2 = "World"
| extend h1 = hash(value1), h2=hash(value2)
| extend combined = hash_combine(h1, h2)
```

|value1|value2|h1|h2|聯合|
|---|---|---|---|---|
|您好|World|753694413698530628|1846988464401551951|-1440138333540407281|
