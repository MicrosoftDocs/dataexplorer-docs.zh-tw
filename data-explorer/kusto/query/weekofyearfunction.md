---
title: 'week_of_year ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文描述 Azure 資料總管中 ( # A1 week_of_year。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 18cad42dfd0f652daa4c8da80524ba40ace9b153
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251241"
---
# <a name="week_of_year"></a>week_of_year ( # A1

傳回代表周數的整數。 周數是從一年中的第一周算算而來的，也就是包含第一個星期四的 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Week_dates)。

```kusto
week_of_year(datetime("2015-12-14"))
```

## <a name="syntax"></a>語法

`week_of_year(`*a_date*`)`

## <a name="arguments"></a>引數

* `a_date`：`datetime`。

## <a name="returns"></a>傳回

`week number` -包含指定日期的周數。

## <a name="examples"></a>範例

|輸入                                    |輸出|
|-----------------------------------------|------|
|`week_of_year(datetime(2020-12-31))`     |`53`  |
|`week_of_year(datetime(2020-06-15))`     |`25`  |
|`week_of_year(datetime(1970-01-01))`     |`1`   |
|`week_of_year(datetime(2000-01-01))`     |`52`  |

> [!NOTE]
> `weekofyear()` 這是這個函式的過時變異。 `weekofyear()` 不符合 ISO 8601 規範;年度的第一周定義為一周，其中年度的第一個星期三。
這個函式的目前版本 `week_of_year()` 是符合 ISO 8601 標準; 一年中的第一周定義為一周，其中年份的第一個星期四。