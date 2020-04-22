---
title: 純量資料類型 - Azure 資料總管 | Microsoft Docs
description: 本文說明 Azure 資料總管中的純量資料類型。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: 3ef87217beee62fe4cecf7ee95dfe8daba49af7a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81490237"
---
# <a name="scalar-data-types"></a>純量資料類型

每個資料值 (例如運算式的值，或函式的參數，或運算式的值) 都具有**資料類型**。 資料類型為**純量資料類型** (下列其中一個內建預先定義的類型)，或為**使用者定義的記錄** (名稱/純量資料類型配對的排序次序，例如資料表資料列的資料類型)。

Kusto 會提供一組系統資料類型，以定義可搭配 Kusto 使用的所有資料類型。

> [!NOTE]
> Kusto 不支援使用者定義的資料類型。

下表列出 Kusto 支援的資料類型，以及可用於參考其的其他別名和大致相同的 .NET Framework 類型。

| 類型       | 其他名稱   | 對等的 .NET 類型              | gettype()   |儲存體類型 (內部名稱)|
| ---------- | -------------------- | --------------------------------- | ----------- |----------------------------|
| `bool`     | `boolean`            | `System.Boolean`                  | `int8`      |`I8`                        |
| `datetime` | `date`               | `System.DateTime`                 | `datetime`  |`DateTime`                  |
| `dynamic`  |                      | `System.Object`                   | `array` 或 `dictionary` 或任何其他值 |`Dynamic`|
| `guid`     | `uuid`, `uniqueid`   | `System.Guid`                     | `guid`      |`UniqueId`                  |
| `int`      |                      | `System.Int32`                    | `int`       |`I32`                       |
| `long`     |                      | `System.Int64`                    | `long`      |`I64`                       |
| `real`     | `double`             | `System.Double`                   | `real`      |`R64`                       |
| `string`   |                      | `System.String`                   | `string`    |`StringBuffer`              |
| `timespan` | `time`               | `System.TimeSpan`                 | `timespan`  |`TimeSpan`                  |
| `decimal`  |                      | `System.Data.SqlTypes.SqlDecimal` | `decimal`   | `Decimal`                  |

所有資料類型都包含特殊的 "null" 值，這表示缺少資料或資料不符 例如，嘗試將字串 `"abc"` 內嵌到 `int` 資料行時，會產生這個值。
您無法明確將此值具體化，但可以使用 `isnull()` 函式偵測運算式是否評估為此值。

> [!WARNING]
> 在撰寫本文時，`guid`類型的支援不完整。 我們強烈建議小組改用 `string` 類型的值。

