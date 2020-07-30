---
title: toguid （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 toguid （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a2eec6a582c4c8fc6cda6b3cf9a304f41ab48143
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350720"
---
# <a name="toguid"></a>toguid()

將輸入轉換為 [`guid`](./scalar-data-types/guid.md) 表示。

```kusto
toguid("70fc66f7-8279-44fc-9092-d364d70fce44") == guid("70fc66f7-8279-44fc-9092-d364d70fce44")
```

## <a name="syntax"></a>語法

`toguid(`*Expr*`)`

## <a name="arguments"></a>引數

* *Expr*：將轉換成純量的運算式 [`guid`](./scalar-data-types/guid.md) 。 

## <a name="returns"></a>傳回

如果轉換成功，則結果將會是純量 [`guid`](./scalar-data-types/guid.md) 。
如果轉換不成功，則結果會是 `null` 。

*注意*：可能的話，偏好使用[guid （）](./scalar-data-types/guid.md) 。