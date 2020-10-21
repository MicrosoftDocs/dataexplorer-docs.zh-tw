---
title: 'binary_shift_right ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文描述 Azure 資料總管中 ( # A1 binary_shift_right。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d33ecb954a7e1e6d0c9c39bdbf057d284affd22b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243514"
---
# <a name="binary_shift_right"></a>binary_shift_right()

傳回一對數位的二元右移運算。

```kusto
binary_shift_right(x,y) 
```

## <a name="syntax"></a>語法

`binary_shift_right(`*num1* `,`*num2*`)`

## <a name="arguments"></a>引數

* *num1*， *num2*： long 數位。

## <a name="returns"></a>傳回

傳回一組數位的二元右移運算： num1 >>  (num2% 64) 。
如果 n 是負數，則會傳回 Null 值。