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
ms.openlocfilehash: 3d54852577281b66ed7754e419acbabbba989e7c
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346077"
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

**提示**

如果 `evaluate` 前面加上包含複雜篩選的表格式來源，或參考大部分來源資料表資料行的篩選，則偏好使用 [`materialize`](materializefunction.md) 函數。 例如：

```kusto
let MaterializedT = materialize(T);
MaterializedT | evaluate preview(50)
```