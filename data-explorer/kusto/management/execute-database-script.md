---
title: . 執行資料庫腳本-Azure 資料總管
description: 本文說明 Azure 資料總管中的「執行資料庫腳本」功能。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/14/2020
ms.openlocfilehash: 667fcc87a1e301bdcceb227bb99ad70d62f46153
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320820"
---
# <a name="execute-database-script"></a>.execute 資料庫指令碼

在單一資料庫的範圍中執行控制命令的批次。

## <a name="syntax"></a>語法

`.execute` `database` `script`  
[ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ...] `)` ]   
`<|`  
 *控制-命令-腳本*

### <a name="parameters"></a>參數

* *Control-命令-script*：具有一或多個控制命令的文字。
* *資料庫範圍*：腳本會套用於指定為要求內容一部分的 *資料庫範圍* 。

### <a name="optional-properties"></a>選用屬性

| 屬性            | 類型            | 描述                          |
|---------------------|-----------------|---------------------------------------------------------------------------------------------------|
| `ContinueOnErrors`            | `bool`        | 如果設定為 `false` -腳本會在第一個錯誤時停止。 如果設定為 `true` -腳本會繼續執行。 預設：`false`。 |

## <a name="output"></a>輸出

出現在腳本中的每個命令都會報告為輸出資料表中的個別記錄。 每一筆記錄都有下欄欄位：

|輸出參數 |類型 |描述
|---|---|--- 
|OperationId  |Guid |命令的識別碼。
|CommandType  |String |命令的類型。
|CommandText  |String |特定命令的文字。
|結果|String|特定命令執行的結果。
|原因|String|命令執行結果的詳細資訊。

>[!NOTE]
>* 腳本文字可能會在命令之間包含空白行和批註。
>* 命令會依序執行于輸入腳本中的循序執行。
>* 腳本執行為非交易式，而且發生錯誤時不會執行任何回復。 使用時，建議使用等冪形式的命令 `.execute database script` 。
>* 命令的預設行為-第一次發生錯誤時失敗，可以使用屬性引數加以變更。
>* 唯讀控制命令 (`.show` 命令) 不會執行，並以狀態報表 `Skipped` 。

## <a name="example"></a>範例

```kusto
.execute database script <|
//
// Create tables
.create-merge table T(a:string, b:string)
//
// Apply policies
.alter-merge table T policy retention softdelete = 10d 
//
// Create functions
.create-or-alter function
  with (skipvalidation = "true") 
  SampleT1(myLimit: long) { 
    T1 | limit myLimit
}
```

|OperationId|CommandType|CommandText|結果|原因|
|---|---|---|---|---|
|1d28531b-58c8-4023-a5d3-16fa73c06cfa|TableCreate|。 create-merge table T (a:string，b:string) |已完成||
|67d0ea69-baa4-419a-93d3-234c03834360|RetentionPolicyAlter|。 alter-merge table T policy 保留 softdelete = now-10d|已完成||
|0b0e8769-d4e8-4ff9-adae-071e52a650c7|FunctionCreateOrAlter|.create-or-alter 函式<br>with (skipvalidation = "true" ) <br>SampleT1 (myLimit： long) {<br>T1 \| 限制 myLimit<br>}|已完成||
