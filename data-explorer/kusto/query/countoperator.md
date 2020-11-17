---
title: 計數運算子-Azure 資料總管
description: 本文說明 Azure 資料總管中的計數操作員。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 04/16/2020
ms.openlocfilehash: 14895e17d77e3bbf1d98d2d68221930d3f291774
ms.sourcegitcommit: f7bebd245081a5cdc08e88fa4f9a769c18e13e5d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94644615"
---
# <a name="count-operator"></a>count 運算子

傳回輸入記錄集內的記錄數目。

## <a name="syntax"></a>語法

`T | count`

## <a name="arguments"></a>引數

*T*︰要計算記錄數目的表格式資料。

## <a name="returns"></a>傳回

此函式會傳回含有單一記錄且資料行類型為 `long`的資料表。 唯一資料格的值是 T 中的記錄數目。 

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents | count
```

## <a name="see-also"></a>另請參閱

如需 count ( # A1 彙總函式的詳細資訊，請參閱 [count ( # A3 (彙總函式) ](count-aggfunction.md)。
