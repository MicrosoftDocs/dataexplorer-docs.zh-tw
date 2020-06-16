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
ms.openlocfilehash: 75dc0aa0ff23bfb4f08be9fac84fa34cf9526508
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/15/2020
ms.locfileid: "84780621"
---
# <a name="database-cursors"></a>資料庫資料指標

**資料庫資料指標**是一種資料庫層級物件，可讓您多次查詢資料庫。 即使有 `data-append` 或 `data-retention` 作業與查詢平行發生，您仍會得到一致的結果。

資料庫資料指標的設計目的是要解決兩個重要的案例：

* 只要查詢指出「相同的資料集」，就能夠多次重複相同的查詢，並取得相同的結果。

* 能夠進行「剛好一次」查詢。 此查詢只會「看見」先前查詢並未看到的資料，因為資料無法使用。
   查詢可讓您逐一查看資料表中所有最近抵達的資料，而不需要擔心處理相同的記錄兩次，或不小心略過記錄。

資料庫資料指標會以查詢語言表示，做為類型的純量值 `string` 。 實際值應視為不透明，而且除了儲存其值或使用資料指標函數以外，不支援任何作業。

## <a name="cursor-functions"></a>資料指標函數

Kusto 提供三個函式，可協助您執行上述兩個案例：

* [cursor_current （）](../query/cursorcurrent.md)：使用此函數來抓取資料庫資料指標的目前值。
   您可以使用此值做為其他兩個函數的引數。
   此函式也有同義字 `current_cursor()` 。

* [cursor_after （rhs： string）](../query/cursorafterfunction.md)：這個特殊函數可以用於已啟用[IngestionTime 原則](ingestiontime-policy.md)的資料表記錄。 它會傳回類型的純量值 `bool` ，指出記錄的 `ingestion_time()` 資料庫資料指標值是否位於 `rhs` 資料庫資料指標值之後。

* [cursor_before_or_at （rhs： string）](../query/cursorbeforeoratfunction.md)：此特殊函數可以用於已啟用[IngestionTime 原則](ingestiontime-policy.md)的資料表記錄。 它會傳回類型的純量值 `bool` ，指出記錄的 `ingestion_time()` 資料庫資料指標值是否位於 `rhs` 資料庫資料指標值之後。

這兩個特殊函式（ `cursor_after` 和 `cursor_before_or_at` ）也有副作用：使用時，Kusto 會將**資料庫資料指標的目前值**發出至 `@ExtendedProperties` 查詢的結果集。 資料指標的屬性名稱是 `Cursor` ，而其值為單一 `string` 。 

例如：

```json
{"Cursor" : "636040929866477946"}
```

## <a name="restrictions"></a>限制

資料庫資料指標只能與已啟用[IngestionTime 原則](ingestiontime-policy.md)的資料表搭配使用。 這類資料表中的每筆記錄都會與內嵌記錄時生效的資料庫資料指標值相關聯。
因此，可以使用[ingestion_time （）](../query/ingestiontimefunction.md)函數。

除非資料庫至少有一個資料表已定義[IngestionTime 原則](ingestiontime-policy.md)，否則資料庫資料指標物件不會保存任何有意義的值。
此值保證會在參考這類資料表的資料表和查詢中，視需要更新內嵌歷程記錄。 不一定會在其他情況下更新。

內嵌程式會先認可資料，使其可供查詢，而且只會將實際的資料指標值指派給每一筆記錄。 如果您嘗試在使用資料庫資料指標進行內嵌完成後立即查詢資料，則結果可能尚未併入最後新增的記錄，因為它們尚未被指派資料指標值。 此外，即使在之間執行內嵌，也可能會傳回相同的值，因為只有資料指標認可哥以更新其值。

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
