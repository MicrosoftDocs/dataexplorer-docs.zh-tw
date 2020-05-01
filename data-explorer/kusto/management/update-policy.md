---
title: Kusto 更新原則管理-Azure 資料總管
description: 本文說明 Azure 資料總管中的更新原則。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 320824b4f614ea809141167c1284282b1ef641cf
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616553"
---
# <a name="update-policy"></a>更新原則

[更新原則](updatepolicy.md)是一種資料表層級原則物件，可在將資料內嵌至另一個資料表時，自動執行查詢並內嵌其結果。

## <a name="show-update-policy"></a>顯示更新原則

如果`*`當做資料表名稱使用，此命令會傳回指定之資料表的更新原則，或預設資料庫中的所有資料表。

**語法**

* `.show``table` *TableName* TableName `policy``update`
* `.show``table` * *DatabaseName`.` * * TableName `policy``update`
* `.show` `table` `*` `policy` `update`

**傳回**

此命令會傳回一個資料表，其中每個資料表都有一筆記錄，其中包含下列資料行：

|資料行    |類型    |描述                                                                                                                                                           |
|----------|--------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|EntityName|`string`|更新原則定義所在之實體的名稱                                                                                                                |
|原則  |`string`|JSON 陣列，指出為實體定義的所有更新原則，格式為[更新原則物件](updatepolicy.md#the-update-policy-object)|

**範例**

```kusto
.show table DerivedTableX policy update 
```

|EntityName        |原則                                                                                                                                    |
|------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
|[TestDB]。[DerivedTableX]|[{"IsEnabled"： true，"Source"： "MyTableX"，"Query"： "MyUpdateFunction （）"，"IsTransactional"： false，"PropagateIngestionProperties"： false}]|

## <a name="alter-update-policy"></a>改變更新原則

此命令會設定指定之資料表的更新原則。

**語法**

* `.alter``table` *TableName* TableName `policy` *ArrayOfUpdatePolicyObjects* ArrayOfUpdatePolicyObjects `update`
* `.alter``table` *DatabaseName* `update` *ArrayOfUpdatePolicyObjects* *TableName* TableName ArrayOfUpdatePolicyObjects`.` `policy`

*ArrayOfUpdatePolicyObjects*是已定義零或多個更新原則物件的 JSON 陣列。

**注意事項**

1. 建議一個針對更新原則物件的`Query`屬性使用預存函數。
   這可讓您輕鬆只修改函式定義，而不是整個原則物件。

2. 設定時，會在更新原則上執行下列驗證（ `IsEnabled`如果設定為`true`）：
    1. `Source`：資料表應該存在於目標資料庫中。
    2. `Query`: 
        * 架構所定義的架構應符合目標資料表的其中一個。 
        * 查詢必須參考更新原則`source`的資料表。 若要定義*未*參考來源的更新原則查詢，可以藉由在`AllowUnreferencedSourceTable=true` with 屬性中設定（請參閱下列範例），但通常不建議基於效能考慮（這表示對來源資料表進行每次內嵌時，會將不同資料表中的*所有*記錄都視為更新原則執行）。
    3. 原則不會導致在目標資料庫的更新原則鏈中建立迴圈。
    4. 如果`IsTransactional`設定為`true`， `TableAdmin`則也會對`Source` （來源資料表）驗證許可權。
  
3. 請務必先測試您的更新原則/函式以取得效能，然後再將它套用到來源資料表的每個內嵌上執行-請參閱[這裡](updatepolicy.md#testing-an-update-policys-performance-impact)。

**傳回**

命令會設定資料表的更新原則物件（覆寫任何目前已定義的原則，如果有的話），然後傳回對應的[. 顯示資料表資料表更新原則](#show-update-policy)命令的輸出。

**範例**

```kusto
// Creating function that will be used for update
.create function 
MyUpdateFunction()
{
    MyTableX
    | where ColumnA == 'some-string'
    | summarize MyCount=count() by ColumnB, Key=ColumnC
    | join (OtherTable | project OtherColumnZ, Key=OtherColumnC) on Key
    | project ColumnB, ColumnZ=OtherColumnZ, Key, MyCount
}

// Creating the target table (in case it doesn't already exist)
.set-or-append DerivedTableX <| MyUpdateFunction() | limit 0

// Use update policy on table DerivedTableX
.alter table DerivedTableX policy update
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyUpdateFunction()", "IsTransactional": false, "PropagateIngestionProperties": false}]'
```

- 當內嵌至來源資料表（在此案例`MyTableX`中為）時，會在該資料表中建立1個或多個範圍（資料分區）。
- 在`Query`更新原則物件中定義的（在此案例`MyUpdateFunction()`中為）只會在那些範圍上執行，而且不會在整個資料表上執行。
  - 此「範圍」是在內部和自動完成，不應在定義時處理`Query`。
  - 內嵌至衍生資料表（在此案例`DerivedTableX`中為）時，只會考慮新的內嵌記錄（每個內嵌作業中的不同）。


```kusto
// The following example will throw an error for not referencing the source table in the update policy query
// The policy's source table is MyTableX, whereas the query only references MyOtherTable. 
.alter table DerivedTableX policy update
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyOtherTable", "IsTransactional": false, "PropagateIngestionProperties": false}]'

// Adding the following properties will suppress the error (but is not recommended)
.alter table DerivedTableX policy update with (AllowUnreferencedSourceTable=true)
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyOtherTable", "IsTransactional": false, "PropagateIngestionProperties": false}]'

```

## <a name="alter-merge-table-table-policy-update"></a>。 alter-merge 資料表資料表原則更新

此命令會修改指定之資料表的更新原則。

**語法**

* `.alter-merge``table` *TableName* TableName `policy` *ArrayOfUpdatePolicyObjects* ArrayOfUpdatePolicyObjects `update`
* `.alter-merge``table` *DatabaseName* `update` *ArrayOfUpdatePolicyObjects* *TableName* TableName ArrayOfUpdatePolicyObjects`.` `policy`

*ArrayOfUpdatePolicyObjects*是已定義零或多個更新原則物件的 JSON 陣列。

**注意事項**

1. 建議使用預存函數來進行更新原則物件之 query 屬性的大量執行。 這可讓您輕鬆只修改函式定義，而不是整個原則物件。

2. 針對`alter` `alter-merge`命令執行命令時，會在更新原則上執行相同的驗證。

**傳回**

此命令會將附加至資料表的更新原則物件（覆寫任何目前已定義的原則，如果有的話），然後傳回對應的 [[顯示資料表資料表更新原則](#show-update-policy)] 命令的輸出。

**範例**

```kusto
.alter-merge table DerivedTableX policy update 
@'[{"IsEnabled": true, "Source": "MyTableY", "Query": "MyUpdateFunction()", "IsTransactional": false}]'  
``` 

## <a name="delete-table-table-policy-update"></a>。刪除資料表資料表原則更新

刪除指定之資料表的更新原則。

**語法**

* `.delete``table` *TableName* TableName `policy``update`
* `.delete``table` * *DatabaseName`.` * * TableName `policy``update`

**傳回**

此命令會刪除資料表的更新原則物件，然後傳回對應的 [[顯示資料表資料表更新原則](#show-update-policy)] 命令的輸出。

**範例**

```kusto
.delete table DerivedTableX policy update 
```