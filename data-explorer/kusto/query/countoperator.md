---
title: count 運算子-Azure 資料總管
description: 本文說明 Azure 資料總管中的 count 運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/16/2020
ms.openlocfilehash: 969efc142f1cd823b319a5c98494542fb2603f24
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348729"
---
# <a name="count-operator"></a>count 運算子

傳回輸入記錄集中的記錄數目。

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
