---
title: 透過 TDS 的 KQL - Azure 資料資源管理員 |微軟文件
description: 本文介紹了 Azure 資料資源管理器中通過 TDS 的 KQL。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/09/2019
ms.openlocfilehash: 364db99141c528f4a822692124ebb870d7315bca
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523302"
---
# <a name="kql-over-tds"></a>KQL over TDS

Kusto 允許利用 TDS 終結點執行以本機[KQL](../../query/index.md)查詢語言創作的查詢。 此功能提供向 Kusto 方向更平滑的升級。 例如,可以創建 SSIS 作業來使用 KQL 查詢查詢 Kusto。

## <a name="executing-kusto-stored-functions"></a>執行函式庫的函式

Kusto 允許執行[儲存的函數](../../query/schema-entities/stored-functions.md),類似於呼叫 SQL 儲存過程。

例如,儲存的功能 MyFunction:

|名稱 |參數|body|資料夾|文件字串
|---|---|---|---|---
|我的功能 |(我的限制:長)| [風暴事件&#124;限制我的限制]|我的資料夾|帶參數的簡報||

可以這樣呼叫:

```csharp
  using (var connection = new SqlConnection(csb.ToString()))
  {
    await connection.OpenAsync();
    using (var command = new SqlCommand("kusto.MyFunction", connection))
    {
      command.CommandType = CommandType.StoredProcedure;
      var parameter = new SqlParameter("mylimit", SqlDbType.Int);
      command.Parameters.Add(parameter);
      parameter.Value = 3;
      using (var reader = await command.ExecuteReaderAsync())
      {
        // Read the response.
      }
    }
  }
```

請注意,存儲函數應使用名為`kusto`的 顯式架構調用,以區分 Kusto 儲存函數和類比 SQL 系統儲存過程。

Kusto 儲存的函數也可以從 T-SQL 呼叫,就像 SQL 表格函數一樣:

```sql
SELECT * FROM kusto.MyFunction(10)
```

建議創建優化的 KQL 查詢並將其封裝在儲存的函數中,從而將 T-SQL 查詢代碼降至最低。

## <a name="executing-kql-query"></a>執行 KQL 查詢

與 SQL`sp_executesql`伺服器類似,Kusto`sp_execute_kql`引入了用於執行[KQL](../../query/index.md)查詢的儲存過程,包括參數化查詢。

的第一個參數`sp_execute_kql`是 KQL 查詢。 可以引入其他參數,它們將像[查詢參數](../../query/queryparametersstatement.md)一樣作用。

例如：

```csharp
  using (var connection = new SqlConnection(csb.ToString()))
  {
    await connection.OpenAsync();
    using (var command = new SqlCommand("sp_execute_kql", connection))
    {
      command.CommandType = CommandType.StoredProcedure;
      var query = new SqlParameter("@kql_query", SqlDbType.NVarChar);
      command.Parameters.Add(query);
      var parameter = new SqlParameter("mylimit", SqlDbType.Int);
      command.Parameters.Add(parameter);
      query.Value = "StormEvents | limit myLimit";
      parameter.Value = 3;
      using (var reader = await command.ExecuteReaderAsync())
      {
        // Read the response.
      }
    }
  }
```

請注意,通過 TDS 調用時無需聲明參數,因為參數類型是通過協定設置的。