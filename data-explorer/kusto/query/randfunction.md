---
title: rand （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 rand （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 53fc7512c1a0fb2019526f48ade54fabbd351d05
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345924"
---
# <a name="rand"></a>rand()

傳回亂數字。

```kusto
rand()
rand(1000)
```

## <a name="syntax"></a>語法

* `rand()`-傳回類型的值 `real` ，其範圍為 [0.0，1.0）的統一分佈。
* `rand(`*N* `)` -傳回所選類型的值 `real` ，其中包含來自集合 {0.0，1.0，...， *N* -1} 的統一散發。