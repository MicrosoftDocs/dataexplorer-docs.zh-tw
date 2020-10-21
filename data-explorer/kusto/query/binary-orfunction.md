---
title: 'binary_or ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文描述 Azure 資料總管中 ( # A1 binary_or。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 97144b244fb6fea5ac218f6160d8aa9e95f50aa4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247884"
---
# <a name="binary_or"></a>binary_or()

傳回 `or` 兩個值的位運算結果。 

```kusto
binary_or(x,y)
```

## <a name="syntax"></a>語法

`binary_or(`*num1* `,`*num2*`)`

## <a name="arguments"></a>引數

* *num1*， *num2*： long 數位。

## <a name="returns"></a>傳回

傳回一對數位的邏輯 OR 運算： num1 |num2.