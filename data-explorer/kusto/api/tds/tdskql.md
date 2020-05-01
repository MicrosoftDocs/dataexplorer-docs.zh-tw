---
title: 透過 TDS 的 KQL-Azure 資料總管 |Microsoft Docs
description: 本文說明在 Azure 資料總管中透過 TDS 的 KQL。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/09/2019
ms.openlocfilehash: 2c4443c0a9301dbc6bb3e65392163da0cc237f74
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617879"
---
# <a name="kql-over-tds"></a>KQL over TDS

Kusto 可讓 TDS 端點執行以原生[KQL](../../query/index.md)查詢語言撰寫的查詢。 這種功能可讓您更順利地進行 Kusto 的遷移。 例如，您可以使用 KQL 查詢建立 SSIS 作業來查詢 Kusto。

## <a name="executing-kusto-stored-functions"></a>執行 Kusto 預存函數

Kusto 允許[預存函數](../../query/schema-entities/stored-functions.md)執行，例如呼叫 SQL 預存程式。

例如，預存函數 MyFunction：

|名稱 |參數|body|資料夾|DocString
|---|---|---|---|---
|MyFunction |（myLimit： long）| {StormEvents &#124; 限制 myLimit}|MyFolder|使用參數的示範函式||

可呼叫的方式如下：

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

> [!注意：] 使用名為`kusto`的明確架構來呼叫預存函式，以區別 Kusto 儲存的函數和模擬的 SQL 系統預存程式。

您也可以從 T-sql 呼叫 Kusto 預存函數，例如 SQL 表格式函數：

```sql
SELECT * FROM kusto.MyFunction(10)
```

建立優化的 KQL 查詢，並將它們封裝在預存函式中，使 T-SQL 查詢程式碼最小。

## <a name="executing-kql-query"></a>執行 KQL 查詢

預存程式`sp_execute_kql`會執行[KQL](../../query/index.md)查詢（包括參數化查詢）。 此程式類似于 SQL server `sp_executesql`。

的第一個參數`sp_execute_kql`是 KQL 查詢。 您可以引進其他參數，它們的作用就像[查詢參數](../../query/queryparametersstatement.md)一樣。

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

> [!注意：] 透過 TDS 呼叫時，不需要宣告參數，因為參數類型是透過通訊協定所設定。
