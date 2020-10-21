---
title: 'binary_and ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文描述 Azure 資料總管中 ( # A1 binary_and。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 93318785cff98c7ca024b638e5e90f58cd9cd9d6
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249361"
---
# <a name="binary_and"></a>binary_and()

傳回 `and` 兩個值之間的位運算結果。

```kusto
binary_and(x,y) 
```

## <a name="syntax"></a>語法

`binary_and(`*num1* `,`*num2*`)`

## <a name="arguments"></a>引數

* *num1*， *num2*： long 數位。

## <a name="returns"></a>傳回

傳回一對數位的邏輯 AND 運算： num1 & num2。