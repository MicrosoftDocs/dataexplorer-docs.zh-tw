---
title: 管理 (控制命令) 概觀 - Azure 資料總管 | Microsoft Docs
description: 本文說明 Azure 資料總管中的管理 (控制命令) 概觀。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1277de1be577de7f9f6d4adf1b74460eac4a8c42
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81490339"
---
# <a name="management-control-commands-overview"></a>管理 (控制命令) 概觀

本節說明用來管理 Kusto 的控制命令。
控制命令是服務的要求，用於擷取資料庫資料表中非必要資料的資訊，或修改服務狀態等。

## <a name="differentiating-control-commands-from-queries"></a>區分控制項命令與查詢

Kusto 使用三種機制來區分查詢和控制命令：在語言層級、在通訊協定層級，以及在 API 層級。 這是為了安全起見而這麼做。

在語言層級，要求文字的第一個字元會決定要求為控制命令或查詢。 控制命令的開頭必須是點 (`.`) 字元，而且沒有任何查詢可以該字元開頭。

在通訊協定層級，不同的 HTTP/HTTPS 端點用於控制命令，而不是查詢。

在 API 層級，不同的函式用來傳送控制命令，而不是查詢。

## <a name="combining-queries-and-control-commands"></a>結合查詢與控制命令

控制命令可以參考查詢 (但不是反之亦然) 或其他控制命令。
有數個支援的案例：

1. **AdminThenQuery**：執行控制命令，並將其結果 (以暫存資料表表示) 當作查詢的輸入提供。
2. **AdminFromQuery**：執行查詢或 `.show` 管理命令，並將其結果 (以暫存資料表表示) 當作控制命令的輸入提供。

請注意，在所有情況下，整個組合在技術上都是控制命令，而不是查詢，因此要求的文字必須以點 (`.`) 字元開頭，且要求必須傳送至服務的管理端點。

另請注意，[查詢陳述式](../query/statements.md)會出現在文字的查詢組件內 (不能在命令本身前面)。

**AdminThenQuery** 會以下列兩種方式的其中一種來表示：

1. 藉由使用分隔號 (`|`) 字元，查詢就會將控制命令的結果視為任何其他資料產生查詢運算子。
2. 藉由使用分號 (`;`) 字元，其接著會將控制命令的結果引入名為 `$command_results` 的特殊符號，然後在查詢中使用任何次數。

例如：

```kusto
// 1. Using pipe: Count how many tables are in the database-in-scope:
.show tables
| count

// 2. Using semicolon: Count how many tables are in the database-in-scope:
.show tables;
$command_results
| count

// 3. Using semicolon, and including a let statement:
.show tables;
let useless=(n:string){strcat(n,'-','useless')};
$command_results | extend LastColumn=useless(TableName)
```

**AdminFromQuery** 是以 `<|`字元組合表示。 例如，我們會在下面先執行一個查詢，它會產生具有單一資料行 (名為 `str` 且為 `string` 類型) 和單一資料列的資料表，並將它撰寫為資料庫內容中的資料表名稱 `MyTable`：

```kusto
.set MyTable <|
let text="Hello, World!";
print str=Text
```
