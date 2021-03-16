---
title: 純量資料類型 - Azure 資料總管 | Microsoft Docs
description: 本文說明 Azure 資料總管中的純量資料類型。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: a7508866f85bb7edb5a6feee5cfe9d191b946a09
ms.sourcegitcommit: c09cc374d5d1d8b396c466ef397690b4b7e4174f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/15/2021
ms.locfileid: "103481550"
---
# <a name="scalar-data-types"></a>純量資料類型

每個資料值 (例如運算式的值，或函式的參數，或運算式的值) 都具有 **資料類型**。 資料類型為 **純量資料類型** (下列其中一個內建預先定義的類型)，或為 **使用者定義的記錄** (名稱/純量資料類型配對的排序次序，例如資料表資料列的資料類型)。

Kusto 會提供一組系統資料類型，以定義可搭配 Kusto 使用的所有資料類型。

> [!NOTE]
> Kusto 不支援使用者定義的資料類型。

下表列出 Kusto 支援的資料類型，以及可用於參考其的其他別名和大致相同的 .NET Framework 類型。

| 類型       | 其他名稱   | 對等的 .NET 類型              | gettype()   |
| ---------- | -------------------- | --------------------------------- | ----------- |
| `bool`     | `boolean`            | `System.Boolean`                  | `int8`      |
| `datetime` | `date`               | `System.DateTime`                 | `datetime`  |
| `dynamic`  |                      | `System.Object`                   | `array` 或 `dictionary` 或任何其他值 |
| `guid`     |                      | `System.Guid`                     | `guid`      |
| `int`      |                      | `System.Int32`                    | `int`       |
| `long`     |                      | `System.Int64`                    | `long`      |
| `real`     | `double`             | `System.Double`                   | `real`      |
| `string`   |                      | `System.String`                   | `string`    |
| `timespan` | `time`               | `System.TimeSpan`                 | `timespan`  |
| `decimal`  |                      | `System.Data.SqlTypes.SqlDecimal` | `decimal`   |

所有非字串資料類型都包含特殊的 "null" 值，這表示缺少資料或資料不符。 例如，嘗試將字串 `"abc"` 內嵌到 `int` 資料行時，會產生這個值。
您無法明確將此值具體化，但可以使用 `isnull()` 函式偵測運算式是否評估為此值。

> [!WARNING]
> `guid` 類型的支援不完整。
> 我們強烈建議小組改用 `string` 類型的值。
