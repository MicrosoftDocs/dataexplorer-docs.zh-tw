---
title: 'rand ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 rand ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1206f72b3951601c105de450bc6923861ad011ae
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241288"
---
# <a name="rand"></a>rand()

傳回亂數字。

```kusto
rand()
rand(1000)
```

## <a name="syntax"></a>語法

* `rand()` -傳回 `real` 具有範圍 [0.0，1.0) 之統一分佈的型別值。
* `rand(`*N* `)` -傳回 `real` 從集合 {0.0，1.0，...， *n-1* } 的統一分佈所選擇之類型的值。