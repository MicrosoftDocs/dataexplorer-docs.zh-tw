---
title: binary_or （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 binary_or （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6903ee36e29551e7af6d08e686c1189e0c0125f3
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349052"
---
# <a name="binary_or"></a>binary_or()

傳回 `or` 兩個值之位運算的結果。 

```kusto
binary_or(x,y)
```

## <a name="syntax"></a>語法

`binary_or(`*num1* `,`*num2*`)`

## <a name="arguments"></a>引數

* *num1*， *num2*： long 數位。

## <a name="returns"></a>傳回

傳回一對數位的邏輯 OR 運算： num1 |num2.