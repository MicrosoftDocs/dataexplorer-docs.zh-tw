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
ms.openlocfilehash: bf595e27d7b4881dca7b5e2a370c90a8407a8c78
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252592"
---
# <a name="count-operator"></a>count 運算子

傳回輸入記錄集內的記錄數目。

## <a name="syntax"></a>語法

`T | count`

## <a name="arguments"></a>引數

*T*︰要計算記錄數目的表格式資料。

## <a name="returns"></a>傳回

此函式會傳回含有單一記錄且資料行類型為 `long`的資料表。 唯一資料格的值是 T ** 中的記錄數目。 

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents | count
```
