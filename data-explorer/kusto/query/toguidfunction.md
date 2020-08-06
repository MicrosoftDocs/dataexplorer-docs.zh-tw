---
title: 'toguid ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 toguid ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 689ee6bf7b3fcb27dced20b06a9002659622902e
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87804129"
---
# <a name="toguid"></a>toguid()

將輸入轉換為 [`guid`](./scalar-data-types/guid.md) 表示。

```kusto
toguid("70fc66f7-8279-44fc-9092-d364d70fce44") == guid("70fc66f7-8279-44fc-9092-d364d70fce44")
```

> [!NOTE]
> 可能的話，最好使用[guid ( # B1](./scalar-data-types/guid.md) 。

## <a name="syntax"></a>語法

`toguid(`*Expr*`)`

## <a name="arguments"></a>引數

* *Expr*：將轉換成純量的運算式 [`guid`](./scalar-data-types/guid.md) 。 

## <a name="returns"></a>傳回

如果轉換成功，則結果將會是純量 [`guid`](./scalar-data-types/guid.md) 。
如果轉換不成功，則結果會是 `null` 。
