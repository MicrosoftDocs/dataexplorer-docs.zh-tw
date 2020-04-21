---
title: 計數運算子 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的計數運算符。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/16/2020
ms.openlocfilehash: c0d0286919a68b6e58065e0a6fe7e0d24b1cdd5f
ms.sourcegitcommit: c4aea69fafa9d9fbb814764eebbb0ae93fa87897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81610225"
---
# <a name="count-operator"></a>count 運算子

返回輸入記錄集中的記錄數。

**語法**

`T | count`

**引數**

*T*︰要計算記錄數目的表格式資料。

**傳回**

此函式會傳回含有單一記錄且資料行類型為 `long`的資料表。 唯一資料格的值是 T ** 中的記錄數目。 

**範例**

```kusto
StormEvents | count
```
