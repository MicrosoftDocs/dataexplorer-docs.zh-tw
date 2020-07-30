---
title: binary_and （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 binary_and （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 611580aebbfd974377f5a22ec904bbbcdbeb6e3f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349086"
---
# <a name="binary_and"></a>binary_and()

傳回 `and` 兩個值之間位運算的結果。

```kusto
binary_and(x,y) 
```

## <a name="syntax"></a>語法

`binary_and(`*num1* `,`*num2*`)`

## <a name="arguments"></a>引數

* *num1*， *num2*： long 數位。

## <a name="returns"></a>傳回

傳回一對數位的邏輯 AND 運算： num1 & num2。