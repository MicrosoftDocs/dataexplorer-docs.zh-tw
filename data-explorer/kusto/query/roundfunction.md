---
title: round （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 round （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c281d3347e82b429ded187ee142ea13fa7594567
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345737"
---
# <a name="round"></a>round()

傳回指定之有效位數的圓角來源。

## <a name="syntax"></a>語法

`round(`*來源*[ `,` *精確度*]`)`

## <a name="arguments"></a>引數

* *來源*：迴圈的計算來源純量。
* *精確度*：來源將四捨五入的位數。（預設值為0）

## <a name="returns"></a>傳回

四捨五入的來源與指定的有效位數。

Round 與不同 [`bin()`](binfunction.md) / [`floor()`](floorfunction.md) 之處在于，第一個會將某個數位四捨五入成特定數目的數位，而最後一個會將值四捨五入為指定之大小時數的整數倍數（round （2.15，1）會傳回2.2，而 bin （2.15，1）則會傳回2）。
 

## <a name="examples"></a>範例

```kusto
round(2.15, 1)                   // 2.2
round(2.15) (which is the same as round(2.15, 0))                   // 2
round(-50.55, -2)                   // -100
round(21.5, -1)                   // 20
```