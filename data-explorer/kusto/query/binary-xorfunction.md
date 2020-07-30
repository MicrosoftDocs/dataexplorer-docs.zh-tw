---
title: binary_xor （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 binary_xor （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6f988fa3d14dab3188bf96825615972995291655
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349001"
---
# <a name="binary_xor"></a>binary_xor()

傳回 `xor` 兩個值之位運算的結果。

```kusto
binary_xor(x,y)
```

## <a name="syntax"></a>語法

`binary_xor(`*num1* `,`*num2*`)`

## <a name="arguments"></a>引數

* *num1*， *num2*： long 數位。

## <a name="returns"></a>傳回

傳回一對數位的邏輯 XOR 運算： num1 ^ num2。