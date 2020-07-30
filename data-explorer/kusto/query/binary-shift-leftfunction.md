---
title: binary_shift_left （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 binary_shift_left （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 46d18bb5d1f661c5346f5ff825c9597088d3f5cf
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349035"
---
# <a name="binary_shift_left"></a>binary_shift_left()

傳回一對數位的 binary 位移 left 運算。

```kusto
binary_shift_left(x,y)  
```

## <a name="syntax"></a>語法

`binary_shift_left(`*num1* `,`*num2*`)`

## <a name="arguments"></a>引數

* *num1*， *num2*： int 數位。

## <a name="returns"></a>傳回

傳回一對數位的 binary 位移 left 運算： num1 <<  （num2% 64）。
如果 n 為負數，則會傳回 Null 值。