---
title: '四捨五入 ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中 ( # A1 的舍入方式。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 90d424929fe0b2034e4778ca2167e1e14dfbf79e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242895"
---
# <a name="round"></a>round()

將圓角來源傳回至指定的有效位數。

## <a name="syntax"></a>語法

`round(`*來源* [有效 `,` *位數*]`)`

## <a name="arguments"></a>引數

* *來源*：計算迴圈的來源純量。
* 有效*位數：來源*將四捨五入至的位數。 (預設值為 0) 

## <a name="returns"></a>傳回

指定之有效位數的舍入來源。

Round 不同于 [`bin()`](binfunction.md) / [`floor()`](floorfunction.md) 在中，第一個會將數位四捨五入到特定的位數，而最後將值四捨五入為指定之大括弧大小的整數倍數 (四捨五入 (2.15，1) 傳回2.2，而 bin (2.15，1) 會傳回 2) 。
 

## <a name="examples"></a>範例

```kusto
round(2.15, 1)                   // 2.2
round(2.15) (which is the same as round(2.15, 0))                   // 2
round(-50.55, -2)                   // -100
round(21.5, -1)                   // 20
```