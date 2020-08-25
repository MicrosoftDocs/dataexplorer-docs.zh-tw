---
title: '解壓縮 ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明如何在 Azure 資料總管中解壓縮 ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 98b0f30c968279fcc757ab49bfda982612379026
ms.sourcegitcommit: 05489ce5257c0052aee214a31562578b0ff403e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88793750"
---
# <a name="extract"></a>extract()

從文字字串取得 [規則運算式](./re2.md) 的相符項目。 

（選擇性）將解壓縮的子字串轉換成指定的類型。

```kusto
extract("x=([0-9.]+)", 1, "hello x=45.6|wo") == "45.6"
```

## <a name="syntax"></a>語法

`extract(`*RegEx* `,`*captureGroup* `,`*文字*[ `,` *typeLiteral*]`)`

## <a name="arguments"></a>引數

* *RegEx*： [正則運算式](./re2.md)。
* *captureGroup*： `int` 指出要解壓縮之捕獲群組的正整數常數。 0 代表整個相符項目、1 代表規則運算式中第一個 '('括號')' 所相符的值，2 或以上的數字代表後續的括號。
* *文字*： `string` 要搜尋的。
* *typeLiteral*：選擇性的型別常值 (例如 `typeof(long)`) 。 如果提供，所擷取的子字串會轉換為此類型。 

## <a name="returns"></a>傳回

如果 regex** 在 text** 中找到相符項目︰針對指定的擷取群組 captureGroup** 進行比對的子字串，可選擇性地轉換為 typeLiteral**。

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