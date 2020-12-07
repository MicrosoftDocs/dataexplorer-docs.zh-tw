---
title: extract() - Azure 資料總管 | Microsoft Docs
description: 本文說明 Azure 資料總管中的 extract()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 483c926d60abef120de2a355a6fa040b9608cd7a
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513041"
---
# <a name="extract"></a>extract()

從文字字串取得 [規則運算式](./re2.md) 的相符項目。 

(選擇性) 將所擷取的子字串轉換為指定的類型。

```kusto
extract("x=([0-9.]+)", 1, "hello x=45.6|wo") == "45.6"
```

## <a name="syntax"></a>語法

`extract(`*regex*`,` *captureGroup*`,` *text* [`,` *typeLiteral*]`)`

## <a name="arguments"></a>引數

* *regex*：[規則運算式](./re2.md)。
* *captureGroup*：指出要擷取之擷取群組的正 `int` 常數。 0 代表整個相符項目、1 代表規則運算式中第一個 '('括號')' 所相符的值，2 或以上的數字代表後續的括號。
* *text*：要搜尋的 `string`。
* *typeLiteral*：選擇性的類型常值 (例如 `typeof(long)`)。 如果提供，所擷取的子字串會轉換為此類型。 

## <a name="returns"></a>傳回

如果 regex 在 text 中找到相符項目︰針對指定的擷取群組 captureGroup 進行比對的子字串，可選擇性地轉換為 typeLiteral。

如果沒有相符項目或類型轉換失敗︰ `null`。 

## <a name="examples"></a>範例

搜尋範例字串 `Trace` 以取得 `Duration` 的定義。 相符項目會轉換為 `real`，然後乘以時間常數 (`1s`)，讓 `Duration` 的類型變為 `timespan`。 在此範例中，結果等於 123.45 秒︰

```kusto
...
| extend Trace="A=1, B=2, Duration=123.45, ..."
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s) 
```

此範例相當於 `substring(Text, 2, 4)`：

```kusto
extract("^.{2,2}(.{4,4})", 1, Text)
```