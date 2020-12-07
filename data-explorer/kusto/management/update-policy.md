---
title: Kusto 更新原則管理-Azure 資料總管
description: 瞭解 Azure 資料總管中的更新原則命令。 請參閱如何顯示、設定、變更和刪除資料表更新原則。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/04/2020
ms.openlocfilehash: 5d346e5b7932437322cb8a41210a6f375cd6d6f0
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321109"
---
# <a name="update-policy-commands"></a>更新原則命令

[更新原則](updatepolicy.md)是一種資料表層級原則物件，它會自動執行查詢，然後在資料內嵌到另一個資料表時，內嵌結果。

## <a name="show-update-policy"></a>顯示更新原則

此命令會傳回指定之資料表的更新原則，如果當做資料表名稱使用，則會傳回預設資料庫中的所有資料表 `*` 。

### <a name="syntax"></a>語法

* `.show` `table` *TableName* `policy` `update`
* `.show``table` *DatabaseName* `.` *TableName* TableName `policy``update`
* `.show` `table` `*` `policy` `update`

### <a name="returns"></a>傳回

此命令會傳回一份資料表，其中每個資料表都有一筆記錄。

|資料行    |類型    |描述                                                                                                                                                           |
|----------|--------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|EntityName|`string`|定義更新原則的機構名稱                                                                                                                |
|原則  |`string`|JSON 陣列，指出針對實體定義的所有更新原則（格式化為[更新原則物件](updatepolicy.md#the-update-policy-object)）|

### <a name="example"></a>範例

```kusto
.show table DerivedTableX policy update 
```

|EntityName        |原則                                                                                                                                    |
|------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
|[TestDB]。[DerivedTableX]|[{"IsEnabled"： true，"Source"： "MyTableX"，"Query"： "MyUpdateFunction ( # A1"，"IsTransactional"： false，"PropagateIngestionProperties"： false}]|

## <a name="alter-update-policy"></a>Alter update 原則

此命令會設定指定之資料表的更新原則。

### <a name="syntax"></a>語法

* `.alter``table` *TableName* `policy` TableName `update`*ArrayOfUpdatePolicyObjects*
* `.alter``table` *DatabaseName* `.` *TableName* `policy` `update` *ArrayOfUpdatePolicyObjects*

*ArrayOfUpdatePolicyObjects* 是已定義零或多個更新原則物件的 JSON 陣列。

> [!NOTE]
> * 針對更新原則物件的屬性使用預存函數 `Query` 。
   您只需要修改函式定義，而不是整個原則物件。
> * 如果 `IsEnabled` 設定為，則會 `true` 在更新原則上執行下列驗證，因為它正在進行設定：
>    * `Source` -檢查資料表是否存在於目標資料庫中。
>    * `Query` 
>        * 檢查架構所定義的架構是否符合其中一個目標資料表
>        * 檢查查詢是否參考 `source` 更新原則的資料表。 
        若要定義不參考來源的更新原則查詢， `AllowUnreferencedSourceTable=true` 您可以在中設定 *with* 屬性 (請參閱下面的範例) ，但由於效能問題，因此不建議這麼做。 針對來源資料表的每個內嵌，會針對更新原則執行考慮不同資料表中的所有記錄。
 >       * 檢查原則不會導致在目標資料庫的更新原則鏈中建立迴圈。
 > * 如果 `IsTransactional` 設定為 `true` ，則會檢查是否 `TableAdmin` 也針對 `Source` (來源資料表) 驗證許可權。
 > * 先測試您的更新原則或函式的效能，再將它套用到來源資料表的每個內嵌上執行。 如需詳細資訊，請參閱 [測試更新原則的效能影響](updatepolicy.md#performance-impact)。

### <a name="returns"></a>傳回

此命令會設定資料表的更新原則物件，並覆寫任何目前的原則，然後傳回對應命令的輸出 [`.show table update policy`](#show-update-policy) 。

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

* 當來源資料表的內嵌發生時，在此情況下 `MyTableX` ，會在該資料表中建立 (資料分區) 的一或多個範圍。
* `Query`在此情況下，更新原則物件中所定義的 `MyUpdateFunction()` 只會在這些範圍執行，而且不會在整個資料表上執行。
  * 「範圍」是在內部和自動完成，而且不應該在定義時處理 `Query` 。
  * 當擷取至衍生資料表時，只會考慮每個內嵌作業中不同的新內嵌記錄 `DerivedTableX` 。

```kusto
// The following example will throw an error for not referencing the source table in the update policy query
// The policy's source table is MyTableX, whereas the query only references MyOtherTable. 
.alter table DerivedTableX policy update
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyOtherTable", "IsTransactional": false, "PropagateIngestionProperties": false}]'

// Adding the following properties will suppress the error (but is not recommended)
.alter table DerivedTableX policy update with (AllowUnreferencedSourceTable=true)
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyOtherTable", "IsTransactional": false, "PropagateIngestionProperties": false}]'

```

## <a name="alter-merge-table-tablename-policy-update"></a>。 alter-合併資料表 *TableName* 原則更新

此命令會修改指定之資料表的更新原則。

**語法**

* `.alter-merge``table` *TableName* `policy` TableName `update`*ArrayOfUpdatePolicyObjects*
* `.alter-merge``table` *DatabaseName* `.` *TableName* `policy` `update` *ArrayOfUpdatePolicyObjects*

*ArrayOfUpdatePolicyObjects* 是已定義零或多個更新原則物件的 JSON 陣列。

> [!NOTE]
> * 使用預存函式進行大量執行更新原則物件的查詢屬性。 
     您只需要修改函式定義，而不是整個原則物件。
> * 驗證與在命令上執行的驗證相同 `alter` 。

**傳回**

此命令會附加至資料表的更新原則物件，並覆寫任何目前的原則，然後傳回對應命令的輸出 [`.show table *TableName* update policy`](#show-update-policy) 。

**範例**

```kusto
.alter-merge table DerivedTableX policy update 
@'[{"IsEnabled": true, "Source": "MyTableY", "Query": "MyUpdateFunction()", "IsTransactional": false}]'  
``` 

## <a name="delete-table-tablename-policy-update"></a>。刪除資料表 *TableName* 原則更新

刪除指定之資料表的更新原則。

**語法**

* `.delete` `table` *TableName* `policy` `update`
* `.delete``table` *DatabaseName* `.` *TableName* TableName `policy``update`

**傳回**

此命令會刪除資料表的更新原則物件，然後傳回對應命令的輸出 [`.show table *TableName* update policy`](#show-update-policy) 。

**範例**

```kusto
.delete table DerivedTableX policy update 
```
