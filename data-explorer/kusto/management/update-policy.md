---
title: 更新策略 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的更新策略。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 95952a6f4e7a8c0d1a5b4207742e15873eb44c91
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519528"
---
# <a name="update-policy"></a>更新原則

[更新策略](updatepolicy.md)是一個表級策略物件,用於在將數據引入另一個表中時自動運行查詢並引入其結果。

## <a name="show-update-policy"></a>顯示更新原則

此命令返回指定表的更新策略,或默認資料庫中的所有表(如果用作表名稱)。" `*`

**語法**

* `.show``table`*表格名稱*`policy``update`
* `.show``table`*資料庫名稱*`.`*表格名稱*`policy``update`
* `.show` `table` `*` `policy` `update`

**傳回**

此指令傳回每個表具有一條記錄的表,該表具有以下欄:

|資料行    |類型    |描述                                                                                                                                                           |
|----------|--------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|EntityName|`string`|更新策略定義的實體名稱                                                                                                                |
|原則  |`string`|JSON 陣列,指示為實體定義的所有更新策略,格式化為[更新策略物件](updatepolicy.md#the-update-policy-object)|

**範例**

```kusto
.show table DerivedTableX policy update 
```

|EntityName        |原則                                                                                                                                    |
|------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
|[測試DB]。[派生表X]|{"啟用":true,"源":"MyTableX","查詢":"我的更新功能()","是事務性":假的,"傳播屬性":false]。|

## <a name="alter-update-policy"></a>變更更新原則

此命令設定指定表的更新策略。

**語法**

* `.alter``table`*TableName*更新`policy`政策物件的 表格列*ArrayOfUpdatePolicyObjects*`update`
* `.alter``table`*資料庫名稱*`.`*表名稱*`policy``update`*更新原則物件的陣列*

*ArrayofUpdatePolicy 對像是*一個 JSON 陣列,它定義了零個或多個更新策略物件。

**注意事項**

1. 建議對更新策略物件`Query`的屬性使用存儲函數。
   這樣可以輕鬆地只修改函數定義,而不是整個策略物件。

2. 在設定更新政策時(以防設定為`IsEnabled``true`)對更新策略執行以下認證( 以防設定為 :
    1. `Source`:表應存在於目標資料庫中。
    2. `Query`: 
        * 架構定義的架構應與目標表之一匹配。 
        * 查詢必須引用更新策略`source`的表。 通過在 with`AllowUnreferencedSourceTable=true`屬性中 設置*不*引用源的更新策略查詢是可能的(請參閱下面的示例),但由於性能原因通常不建議這樣做(這意味著對於源表的每個引入,都考慮執行不同表中*的所有*記錄)。
    3. 該策略不會導致在目標資料庫中的更新策略鏈中創建迴圈。
    4. 如果`IsTransactional`設定為`true`,`TableAdmin`則`Source`對 (源表) 驗證權限。
  
3. 請確保在將更新策略/函數應用於源表的每個引入上執行之前,對其進行效能測試 -[請參閱 此處](updatepolicy.md#testing-an-update-policys-performance-impact)。

**傳回**

該命令設定表的更新策略物件(覆蓋定義的任何當前策略(如果有),然後返回相應的[.show 表 TABLE 更新策略](#show-update-policy)命令的輸出。

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

- 當引入到源表(在本例中`MyTableX`)時,將在此表中創建 1 個或多個擴展盤區(數據分片)。
- `Query`在更新策略物件(本例中`MyUpdateFunction()`)中定義的 將僅在這些擴展盤區上運行,並且不會在整個表上運行。
  - 此「範圍界定」在內部自動完成,在定義`Query`時 不應處理。
  - 引入派生表時(在本例`DerivedTableX`中),只會考慮新引入的記錄(每次引入操作中不同)。


```kusto
// The following example will throw an error for not referencing the source table in the update policy query
// The policy's source table is MyTableX, whereas the query only references MyOtherTable. 
.alter table DerivedTableX policy update
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyOtherTable", "IsTransactional": false, "PropagateIngestionProperties": false}]'

// Adding the following properties will suppress the error (but is not recommended)
.alter table DerivedTableX policy update with (AllowUnreferencedSourceTable=true)
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyOtherTable", "IsTransactional": false, "PropagateIngestionProperties": false}]'

```

## <a name="alter-merge-table-table-policy-update"></a>.更改-合併表表策略更新

此命令修改指定表的更新策略。

**語法**

* `.alter-merge``table`*TableName*更新`policy`政策物件的 表格列*ArrayOfUpdatePolicyObjects*`update`
* `.alter-merge``table`*資料庫名稱*`.`*表名稱*`policy``update`*更新原則物件的陣列*

*ArrayofUpdatePolicy 對像是*一個 JSON 陣列,它定義了零個或多個更新策略物件。

**注意事項**

1. 建議使用存儲的函數來批量實現更新策略物件的查詢屬性。 這樣可以輕鬆地只修改函數定義,而不是整個策略物件。

2. 在`alter`命令的情況下對更新策略執行`alter-merge`相同的驗證。

**傳回**

該命令追加到表的更新策略物件(覆蓋定義的任何當前策略(如果有),然後返回相應的[.show 表表更新策略](#show-update-policy)命令的輸出。

**範例**

```kusto
.alter-merge table DerivedTableX policy update 
@'[{"IsEnabled": true, "Source": "MyTableY", "Query": "MyUpdateFunction()", "IsTransactional": false}]'  
``` 

## <a name="delete-table-table-policy-update"></a>.刪除表表策略更新

刪除指定表的更新策略。

**語法**

* `.delete``table`*表格名稱*`policy``update`
* `.delete``table`*資料庫名稱*`.`*表格名稱*`policy``update`

**傳回**

該命令刪除表的更新策略物件,然後返回相應的[.show 表 TABLE 更新策略](#show-update-policy)命令的輸出。

**範例**

```kusto
.delete table DerivedTableX policy update 
```