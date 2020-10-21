---
title: 預覽外掛程式-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的預覽外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 455ff1d4d3a42c09a39673028405d51b7acd1f5b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251638"
---
# <a name="preview-plugin"></a>preview 外掛程式

從輸入記錄集傳回最多指定資料列數目的資料表，以及輸入記錄集中的記錄總數。

```kusto
T | evaluate preview(50)
```

## <a name="syntax"></a>語法

`T` `|` `evaluate` `preview(` *NumberOfRows* `)`

## <a name="returns"></a>傳回

外掛程式會傳回 `preview` 兩個結果資料表：
* 具有最多指定資料列數目的資料表。
  例如，上述範例查詢相當於正在執行 `T | take 50` 。
* 具有單一資料列/資料行的資料表，其中包含輸入記錄集中的記錄數目。
  例如，上述範例查詢相當於正在執行 `T | count` 。

> [!TIP]
> 如果 `evaluate` 之前是包含複雜篩選的表格式來源，或是參考大部分來源資料表資料行的篩選，則偏好使用 [`materialize`](materializefunction.md) 函數。 例如：

```kusto
let MaterializedT = materialize(T);
MaterializedT | evaluate preview(50)
```