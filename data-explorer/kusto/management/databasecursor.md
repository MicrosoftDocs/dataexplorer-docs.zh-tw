---
title: 資料庫資料指標-Azure 資料總管
description: 本文說明 Azure 資料總管中的資料庫資料指標。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3a3deb388c5a57f3400eb5fbe24f77a31e48b69c
ms.sourcegitcommit: 1bdbfdc04c4eac405f3931059bbeee2dedd87004
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/27/2020
ms.locfileid: "96303306"
---
# <a name="database-cursors"></a>資料庫指標

**資料庫資料指標** 是資料庫層級物件，可讓您多次查詢資料庫。 即使 `data-append` `data-retention` 與查詢平行發生或作業，您仍會得到一致的結果。

資料庫資料指標的設計目的是要解決兩個重要的案例：

* 只要查詢指出「相同的資料集」，就可以多次重複相同的查詢並取得相同的結果。

* 進行「剛好一次」查詢的能力。 此查詢只會「看到」先前查詢未看到的資料，因為資料無法使用。
   此查詢可讓您逐一查看資料表中所有新抵達的資料，而不需要擔心處理相同記錄兩次或略過記錄。

資料庫資料指標以查詢語言表示為類型的純量值 `string` 。 實際的值應視為不透明，而且除了儲存其值或使用下面所述的資料指標函數以外，不支援任何作業。

## <a name="cursor-functions"></a>資料指標函數

Kusto 提供三個函數來協助您執行上述兩個案例：

* [cursor_current ( # B1 ](../query/cursorcurrent.md)：使用此函數來取出資料庫資料指標的目前值。
   您可以使用此值做為其他兩個函數的引數。
   此函數也有同義字 `current_cursor()` 。

* [cursor_after (rhs： string) ](../query/cursorafterfunction.md)：此特殊函數可用於已啟用 [IngestionTime 原則](ingestiontime-policy.md) 的資料表記錄。 它會傳回類型的純量值 `bool` ，指出記錄的 `ingestion_time()` 資料庫資料指標值是否位於 `rhs` 資料庫資料指標值之後。

* [cursor_before_or_at (rhs： string) ](../query/cursorbeforeoratfunction.md)：此特殊函數可用於已啟用 [IngestionTime 原則](ingestiontime-policy.md) 的資料表記錄。 它會傳回類型的純量值 `bool` ，指出記錄的 `ingestion_time()` 資料庫資料指標值是否位於 `rhs` 資料庫資料指標值之前或。

這兩個特殊函數 (`cursor_after` 和 `cursor_before_or_at`) 也有副作用：使用時，Kusto 會將 **資料庫資料指標目前的值** 發出至查詢的 `@ExtendedProperties` 結果集。 資料指標的屬性名稱為 `Cursor` ，且其值為 single `string` 。 

例如：

```json
{"Cursor" : "636040929866477946"}
```

## <a name="restrictions"></a>限制

資料庫資料指標只能與已啟用 [IngestionTime 原則](ingestiontime-policy.md) 的資料表搭配使用。 這類資料表中的每筆記錄都與內嵌記錄時生效的資料庫資料指標值相關聯。
因此，可以使用 [ingestion_time ( # B1 ](../query/ingestiontimefunction.md) 函數。

除非資料庫至少有一個資料表已定義 [IngestionTime 原則](ingestiontime-policy.md) ，否則資料庫資料指標物件不會保存任何有意義的值。
此值保證會在參考這類資料表的這類資料表和查詢執行時，視需要更新內嵌歷程記錄。 它可能會在其他情況下更新，或可能不會更新。

內嵌進程會先認可資料，使其可供查詢，而且只會將實際的資料指標值指派給每一筆記錄。 如果您嘗試使用資料庫資料指標來立即查詢內嵌完成後的資料，結果可能還未併入最後新增的記錄，因為它們尚未被指派資料指標值。 此外，即使在之間進行內嵌，也可能會傳回相同的值，因為只有資料指標認可哥以更新其值。

## <a name="example-processing-records-exactly-once"></a>範例：只處理一次記錄

針對 `Employees` 具有架構的資料表 `[Name, Salary]` ，若要在內嵌至資料表時持續處理新記錄，請使用下列程式：

```kusto
// [Once] Enable the IngestionTime policy on table Employees
.set table Employees policy ingestiontime true

// [Once] Get all the data that the Employees table currently holds 
Employees | where cursor_after('')

// The query above will return the database cursor value in
// the @ExtendedProperties result set. Lets assume that it returns
// the value '636040929866477946'

// [Many] Get all the data that was added to the Employees table
// since the previous query was run using the previously-returned
// database cursor 
Employees | where cursor_after('636040929866477946') // -> 636040929866477950

Employees | where cursor_after('636040929866477950') // -> 636040929866479999

Employees | where cursor_after('636040929866479999') // -> 636040939866479000
```
