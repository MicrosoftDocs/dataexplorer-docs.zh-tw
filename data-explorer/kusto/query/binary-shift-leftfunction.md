---
title: 'binary_shift_left ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文描述 Azure 資料總管中 ( # A1 binary_shift_left。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9f01f0178fa850009f6298446b02c9d4bd913bf8
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252611"
---
# <a name="binary_shift_left"></a>binary_shift_left()

傳回一對數位的二進位左移作業。

```kusto
binary_shift_left(x,y)  
```

## <a name="syntax"></a>語法

`binary_shift_left(`*num1* `,`*num2*`)`

## <a name="arguments"></a>引數

* *num1*， *num2*： int 數位。

## <a name="returns"></a>傳回

傳回一對數位的二進位左移作業： num1 <<  (num2% 64) 。
如果 n 是負數，則會傳回 Null 值。