---
title: week_of_year() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的week_of_year()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 1c3702165f01ab321f80bad900e59968092be2ed
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504534"
---
# <a name="week_of_year"></a>week_of_year()

返回表示周數的整數。 根據[ISO 8601,](https://en.wikipedia.org/wiki/ISO_8601#Week_dates)周數是從一年的第一周計算的,這是包括第一個星期四的周數。

```kusto
week_of_year(datetime("2015-12-14"))
```

**語法**

`week_of_year(`*a_date*`)`

**引數**

* `a_date`：`datetime`。

**傳回**

`week number`- 包含給定日期的周編號。

**範例**

|輸入                                    |輸出|
|-----------------------------------------|------|
|`week_of_year(datetime(2020-12-31))`     |`53`  |
|`week_of_year(datetime(2020-06-15))`     |`25`  |
|`week_of_year(datetime(1970-01-01))`     |`1`   |
|`week_of_year(datetime(2000-01-01))`     |`52`  |

> [!NOTE]
> `weekofyear()`是此函數的過時變體。 `weekofyear()`不符合 ISO 8601 標準;一年的第一周被定義為一年的第一個星期。
此函數的當前版本`week_of_year()`, 符合 ISO 8601 標準;一年的第一周被定義為一年的第一個星期。