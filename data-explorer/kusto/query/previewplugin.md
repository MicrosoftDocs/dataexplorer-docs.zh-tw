---
title: 預覽外掛程式-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的預覽外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7c4ee69c4f82c25c6f4cf7d4b63ad9a659892a28
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87802990"
---
# <a name="preview-plugin"></a>preview 外掛程式

傳回資料表，其中包含輸入記錄集內的指定資料列數目，以及輸入記錄集中的記錄總數。

```kusto
T | evaluate preview(50)
```

## <a name="syntax"></a>語法

`T` `|` `evaluate` `preview(` *NumberOfRows* `)`

## <a name="returns"></a>傳回

外掛程式會傳回 `preview` 兩個結果資料表：
* 最多包含指定資料列數目的資料表。
  例如，上述範例查詢相當於 [執行中] `T | take 50` 。
* 含有單一資料列/資料行的資料表，其中包含輸入記錄集中的記錄數目。
  例如，上述範例查詢相當於 [執行中] `T | count` 。

> [!TIP]
> 如果 `evaluate` 前面加上包含複雜篩選的表格式來源，或參考大部分來源資料表資料行的篩選，則偏好使用 [`materialize`](materializefunction.md) 函數。 例如：

```kusto
let MaterializedT = materialize(T);
MaterializedT | evaluate preview(50)
```