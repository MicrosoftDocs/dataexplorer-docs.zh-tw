---
title: week_of_year （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 week_of_year （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 82678a68166061fc7b8a30c7cb2e019c8d3d9e0c
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87338553"
---
# <a name="week_of_year"></a>week_of_year （）

傳回代表周數的整數。 周數是根據[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Week_dates)，從一年的第一周計算，也就是其中包含第一個星期四的數位。

```kusto
week_of_year(datetime("2015-12-14"))
```

## <a name="syntax"></a>語法

`week_of_year(`*a_date*`)`

## <a name="arguments"></a>引數

* `a_date`：`datetime`。

## <a name="returns"></a>傳回

`week number`-包含指定日期的周數。

## <a name="examples"></a>範例

|輸入                                    |輸出|
|-----------------------------------------|------|
|`week_of_year(datetime(2020-12-31))`     |`53`  |
|`week_of_year(datetime(2020-06-15))`     |`25`  |
|`week_of_year(datetime(1970-01-01))`     |`1`   |
|`week_of_year(datetime(2000-01-01))`     |`52`  |

> [!NOTE]
> `weekofyear()`是此函式的過時變體。 `weekofyear()`不符合 ISO 8601 規範;一年的第一周定義為一周，其中年份的第一個星期三。
此函式的目前版本 `week_of_year()` 是符合 ISO 8601 標準; 一年的第一周定義為一周，其中第一年的第一個星期四。