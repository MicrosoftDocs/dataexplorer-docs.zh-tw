---
title: 資料擷取() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的數據提取()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 398c79ddcd362f3c09fea18accd082c0eb3b1fc3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515363"
---
# <a name="extract"></a>extract()

從文字字串取得 [規則運算式](./re2.md) 的相符項目。 

可以選擇將提取的子字串轉換為指示的類型。

    extract("x=([0-9.]+)", 1, "hello x=45.6|wo") == "45.6"

**語法**

`extract(`*以正規表示式*`,`*擷取群組*`,``,`*文字*[*類型文字*】`)`

**引數**

* *正規表示式*:[正規表示式](./re2.md)。
* *捕獲組*:`int`指示 要提取的捕獲組的正常量。 0 代表整個相符項目、1 代表規則運算式中第一個 '('括號')' 所相符的值，2 或以上的數字代表後續的括號。
* *文字*`string`: 要搜尋的 。
* *字形*類型 : 可選擇型態`typeof(long)`文字(例如, 如果提供，所擷取的子字串會轉換為此類型。 

**傳回**

如果 regex** 在 text** 中找到相符項目︰針對指定的擷取群組 captureGroup** 進行比對的子字串，可選擇性地轉換為 typeLiteral**。

如果沒有相符項目或類型轉換失敗︰ `null`。 

**範例**

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