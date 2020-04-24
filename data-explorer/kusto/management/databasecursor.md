---
title: 資料庫游標 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的資料庫游標。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 90ec677a7eaf1f326509828b5415b022742fd9ed
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521313"
---
# <a name="database-cursors"></a>資料庫游標

**資料庫游標**是資料庫級物件,即使與查詢並行執行數據追加/數據保留操作,也可以多次查詢資料庫並獲得一致的結果。

資料庫游標旨在解決兩種重要方案:

* 只要查詢指示「同一數據集」,即可多次重複同一查詢並獲取相同的結果。

* 執行「完全一次」查詢(僅」查看「以前查詢由於當時數據不可用而未看到的數據的查詢) 的能力。
   例如,這允許迭代表中所有新到達的數據,而不必擔心處理同一記錄兩次或錯誤地跳過記錄。

資料庫游標在查詢語言中表示為類型的`string`標量值。 實際值應視為不透明,除了保存其值和/或使用下面所述的游標函數之外,沒有任何操作支援。

## <a name="cursor-functions"></a>資料指標函數

Kusto 提供了三個函數來幫助實現上述兩個方案:

* [cursor_current()](../query/cursorcurrent.md): 使用此函數檢索資料庫游標的當前值。
   可以將此值用作其他兩個函數的參數。
   此函數還有同義詞`current_cursor()`。

* [cursor_after(rhs:字串):](../query/cursorafterfunction.md)此特殊函數可用於啟用[引入時間策略](ingestiontime-policy.md)的表記錄。 它返回類型的`bool`標量值,指示記錄`ingestion_time()`的 資料庫游標值`rhs`是否位於資料庫游標值之後。

* [cursor_before_or_at(rhs:字串):](../query/cursorbeforeoratfunction.md)此特殊函數可用於啟用[引入時間策略](ingestiontime-policy.md)的表記錄。 它返回類型的`bool`標量值,指示記錄`ingestion_time()`的 資料庫游標值`rhs`是否位於資料庫游標值之後。

這兩個特殊函數`cursor_after``cursor_before_or_at`( 和 ) 也有副作用:當它們被使用時,Kusto 會將**資料庫游標的當前值**發射`@ExtendedProperties`到查詢的結果集。 游標的屬性名稱稱為`Cursor`,其值為`string`單個 。 例如：

```json
{"Cursor" : "636040929866477946"}
```

## <a name="restrictions"></a>限制

資料庫游標只能與已為其啟用[引入時間策略](ingestiontime-policy.md)的表一起使用。 此類表中的每個記錄都與引入記錄時有效的資料庫游標的值相關聯,因此可以使用[ingestion_time()](../query/ingestiontimefunction.md)函數。

除非資料庫至少有一個表定義了[引入時間策略](ingestiontime-policy.md),否則資料庫游標物件不保存有意義的值。
此外,僅保證此值根據需要由引入歷史記錄更新到此類表中,並且查詢運行引用此類表。 在其他情況下,它可能更新,也可能不更新。

引入過程首先提交數據(以便可用於查詢),然後僅為每個記錄分配實際游標值。 這意味著,如果嘗試使用資料庫游標在引入完成後立即查詢數據,則結果可能尚未包含添加的最後記錄,因為它們尚未分配游標值。 同樣,重複檢索當前資料庫游標值可能會返回相同的值(即使兩者之間進行了引入),因為只有游標提交才會更新其值。

## <a name="example-processing-of-records-exactly-once"></a>範例:完全處理記錄一次

假設具有`Employees`架構`[Name, Salary]`的表。
要在將新記錄引入表中時持續處理新記錄,請使用以下步驟:

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