---
title: Kusto 更新原則管理-Azure 資料總管
description: 本文說明 Azure 資料總管中的更新原則。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/04/2020
ms.openlocfilehash: 111110ac69e726c8367af4a2741a79061df7531a
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803857"
---
# <a name="update-policy-commands"></a>更新原則命令

[更新原則](updatepolicy.md)是一種資料表層級原則物件，會在將資料內嵌到另一個資料表時，自動執行查詢，然後內嵌結果。

## <a name="show-update-policy"></a>顯示更新原則

如果當做資料表名稱使用，此命令會傳回指定之資料表的更新原則，或預設資料庫中的所有資料表 `*` 。

### <a name="syntax"></a>語法

* `.show``table` *TableName* TableName `policy``update`
* `.show``table` *DatabaseName* `.` *TableName* TableName `policy``update`
* `.show` `table` `*` `policy` `update`

### <a name="returns"></a>傳回

此命令會傳回每個資料表有一筆記錄的資料表。

|資料行    |類型    |描述                                                                                                                                                           |
|----------|--------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|EntityName|`string`|更新原則定義所在之實體的名稱                                                                                                                |
|原則  |`string`|JSON 陣列，指出為實體定義的所有更新原則，格式為[更新原則物件](updatepolicy.md#the-update-policy-object)|

### <a name="example"></a>範例

```kusto
.show table DerivedTableX policy update 
```

|EntityName        |原則                                                                                                                                    |
|------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
|[TestDB]。[DerivedTableX]|[{"IsEnabled"： true，"Source"： "MyTableX"，"Query"： "MyUpdateFunction ( # A1"，"IsTransactional"： false，"PropagateIngestionProperties"： false}]|

## <a name="alter-update-policy"></a>改變更新原則

此命令會設定指定之資料表的更新原則。

### <a name="syntax"></a>語法

* `.alter``table` *TableName* `policy` TableName `update`*ArrayOfUpdatePolicyObjects*
* `.alter``table` *DatabaseName* `.` *TableName* `policy` `update` *ArrayOfUpdatePolicyObjects*

*ArrayOfUpdatePolicyObjects*是已定義零或多個更新原則物件的 JSON 陣列。

> [!NOTE]
> * 針對更新原則物件的屬性使用預存函數 `Query` 。
   您只需要修改函式定義，而不是整個原則物件。
> * 如果 `IsEnabled` 設定為，則會 `true` 在所設定的更新原則上執行下列驗證：
>    * `Source`-檢查資料表是否存在於目標資料庫中。
>    * `Query` 
>        * 檢查架構所定義的架構是否符合目標資料表的其中一個
>        * 檢查查詢是否參考 `source` 更新原則的資料表。 
        若要定義未參考來源的更新原則查詢，可以藉由 `AllowUnreferencedSourceTable=true` 在*with*屬性中設定 (查看下列範例) ，但由於效能問題而不建議這麼做。 對於來源資料表的每個內嵌，會將不同資料表中的所有記錄視為更新原則執行。
 >       * 檢查原則不會導致在目標資料庫的更新原則鏈中建立迴圈。
 > * 如果 `IsTransactional` 設定為 `true` ，則會檢查 `TableAdmin` 許可權是否也會針對 `Source` 來源資料表)  (進行驗證。
 > * 測試您的更新原則或函式的效能，然後再將它套用到來源資料表的每個內嵌上執行。 如需詳細資訊，請參閱[測試更新原則對效能的影響](updatepolicy.md#performance-impact)。

### <a name="returns"></a>傳回

此命令會設定資料表的更新原則物件，並覆寫任何目前的原則，然後傳回對應的 [[顯示資料表更新原則](#show-update-policy)] 命令的輸出。

### <a name="example"></a>範例

```kusto
// Create a function that will be used for update
.create function 
MyUpdateFunction()
{
    MyTableX
    | where ColumnA == 'some-string'
    | summarize MyCount=count() by ColumnB, Key=ColumnC
    | join (OtherTable | project OtherColumnZ, Key=OtherColumnC) on Key
    | project ColumnB, ColumnZ=OtherColumnZ, Key, MyCount
}

// Create the target table (if it doesn't already exist)
.set-or-append DerivedTableX <| MyUpdateFunction() | limit 0

// Use update policy on table DerivedTableX
.alter table DerivedTableX policy update
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyUpdateFunction()", "IsTransactional": false, "PropagateIngestionProperties": false}]'
```

* 當發生來源資料表的內嵌時，在此情況下，會 `MyTableX` 在該資料表中建立一或多個 (資料分區) 的範圍
* `Query`在此情況下，更新原則物件中定義的 `MyUpdateFunction()` 只會在那些範圍上執行，而且不會在整個資料表上執行。
  * 「範圍」是在內部和自動完成，不應在定義時處理 `Query` 。
  * 在內嵌至衍生資料表時，只有新內嵌的記錄（在每個內嵌作業中都不同）會納入考慮 `DerivedTableX` 。

```kusto
// The following example will throw an error for not referencing the source table in the update policy query
// The policy's source table is MyTableX, whereas the query only references MyOtherTable. 
.alter table DerivedTableX policy update
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyOtherTable", "IsTransactional": false, "PropagateIngestionProperties": false}]'

// Adding the following properties will suppress the error (but is not recommended)
.alter table DerivedTableX policy update with (AllowUnreferencedSourceTable=true)
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyOtherTable", "IsTransactional": false, "PropagateIngestionProperties": false}]'

```

## <a name="alter-merge-table-tablename-policy-update"></a>。 alter-merge 資料表*TableName*原則更新

此命令會修改指定之資料表的更新原則。

**語法**

* `.alter-merge``table` *TableName* `policy` TableName `update`*ArrayOfUpdatePolicyObjects*
* `.alter-merge``table` *DatabaseName* `.` *TableName* `policy` `update` *ArrayOfUpdatePolicyObjects*

*ArrayOfUpdatePolicyObjects*是已定義零或多個更新原則物件的 JSON 陣列。

> [!NOTE]
> * 針對更新原則物件的查詢屬性，使用預存函數進行大量執行。 
     您只需要修改函式定義，而不是整個原則物件。
> * 驗證與在命令上完成的驗證相同 `alter` 。

**傳回**

命令會附加至資料表的更新原則物件，並覆寫任何目前的原則，然後傳回對應之的輸出[。顯示資料表*TableName*更新原則](#show-update-policy)命令。

**範例**

```kusto
.alter-merge table DerivedTableX policy update 
@'[{"IsEnabled": true, "Source": "MyTableY", "Query": "MyUpdateFunction()", "IsTransactional": false}]'  
``` 

## <a name="delete-table-tablename-policy-update"></a>。刪除資料表*TableName*的原則更新

刪除指定之資料表的更新原則。

**語法**

* `.delete``table` *TableName* TableName `policy``update`
* `.delete``table` *DatabaseName* `.` *TableName* TableName `policy``update`

**傳回**

此命令會刪除資料表的更新原則物件，然後傳回對應之的輸出[。顯示資料表*TableName*更新原則](#show-update-policy)命令。

**範例**

```kusto
.delete table DerivedTableX policy update 
```
