---
title: sql_request 外掛程式-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 sql_request 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 06cb62256b3dc433d375a23991ebc26b4f703c56
ms.sourcegitcommit: 3c86e21bd8a04eca39250a773d0e89ae05065b2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84153636"
---
# <a name="sql_request-plugin"></a>sql_request 外掛程式

::: zone pivot="azuredataexplorer"

  `evaluate``sql_request` `(` *ConnectionString* `,` *SqlQuery* [ `,` *SqlParameters* [ `,` *Options*]]`)`

`sql_request`外掛程式會將 SQL 查詢傳送至 SQL Server 網路端點，並傳回結果中的第一個資料列集。

**引數**

* *ConnectionString*： `string` 指出指向 SQL Server 網路端點之連接字串的常值。 請參閱下文以瞭解有效的驗證方法，以及如何指定網路端點。

* *SqlQuery*： `string` 表示要針對 SQL 端點執行之查詢的常值。 必須傳回一或多個資料列集，但只有第一個資料列集會供 Kusto 查詢的其餘部分使用。

* *SqlParameters*：類型的常數值 `dynamic` ，保留要當做參數傳遞的索引鍵/值組與查詢。 選擇性。
  
* *選項*：類型的常數值 `dynamic` ，可將更多的設定做為索引鍵/值組來保存。 目前僅能 `token` 設定，以傳遞呼叫端提供的 AAD 存取權杖，以轉送至 SQL 端點進行驗證。 選擇性。

**範例**

下列範例會將 SQL 查詢傳送至 Azure SQL DB 資料庫，以從抓取所有記錄，然後在 `[dbo].[Table]` Kusto 端處理結果。 驗證會重複使用呼叫使用者的 AAD 權杖。

注意：此範例不應做為以這種方式篩選/專案資料的建議。 通常最好是將 SQL 查詢結構化以傳回可能的最小資料集，因為目前 Kusto 優化工具不會嘗試優化 Kusto 與 SQL 之間的查詢。

```kusto
evaluate sql_request(
  'Server=tcp:contoso.database.windows.net,1433;'
    'Authentication="Active Directory Integrated";'
    'Initial Catalog=Fabrikam;',
  'select * from [dbo].[Table]')
| where Id > 0
| project Name
```

下列範例與前一個範例相同，不同之處在于 SQL 驗證是以使用者名稱/密碼完成。 請注意，為了保密起見，我們在這裡使用模糊的字串。

```kusto
evaluate sql_request(
  'Server=tcp:contoso.database.windows.net,1433;'
    'Initial Catalog=Fabrikam;'
    h'User ID=USERNAME;'
    h'Password=PASSWORD;',
  'select * from [dbo].[Table]')
| where Id > 0
| project Name
```

下列範例會將 SQL 查詢傳送至 Azure SQL DB 資料庫，以從接收所有記錄 `[dbo].[Table]` ，同時附加另一個資料 `datetime` 行，然後在 Kusto 端處理結果。
它會指定 `@param0` 要在 SQL 查詢中使用的 sql 參數（）。

```kusto
evaluate sql_request(
  'Server=tcp:contoso.database.windows.net,1433;'
    'Authentication="Active Directory Integrated";'
    'Initial Catalog=Fabrikam;',
  'select *, @param0 as dt from [dbo].[Table]',
  dynamic({'param0': datetime(2020-01-01 16:47:26.7423305)}))
| where Id > 0
| project Name
```

**驗證**

Sql_request 外掛程式支援三種對 SQL Server 端點的驗證方法：

* **AAD 整合式驗證**（ `Authentication="Active Directory Integrated"` ）：這是慣用的方法，使用者或應用程式會透過 AAD 向 Kusto 進行驗證，然後使用相同的權杖來存取 SQL Server 網路端點。

* 使用者**名稱/密碼驗證**（ `User ID=...; Password=...;` ）：當 AAD 整合式驗證無法執行時，會提供此方法的支援。 盡可能避免使用此方法，因為秘密資訊是透過 Kusto 傳送。

* **AAD 存取權杖**（ `dynamic({'token': h"eyJ0..."})` ）：使用此驗證方法時，存取權杖是由呼叫者產生，並藉由 KUSTO 轉送到 SQL 端點。 連接字串不應包含驗證資訊，例如 `Authentication` 、 `User ID` 或 `Password` 。 相反地，存取權杖會當做 `token` 屬性，在 `Options` sql_request 外掛程式的引數中傳遞。
     
> [!WARNING]
> 包含機密資訊的連接字串和查詢，或應該受到防護的資訊應該進行混淆，以便從任何 Kusto 追蹤中省略。
> 如需詳細資訊，請參閱[模糊字串常](scalar-data-types/string.md#obfuscated-string-literals)值。

**加密和伺服器驗證**

基於安全考慮，連接到 SQL Server 網路端點時，會強制執行下列連線屬性：

* `Encrypt`設定為 `true` 無條件。
* `TrustServerCertificate`設定為 `false` 無條件。

因此，必須使用有效的 SSL/TLS 伺服器憑證來設定 SQL Server。

**指定網路端點**

將 SQL 網路端點指定為連接字串的一部分是必要的。
適當的語法為：

`Server``=` `tcp:` *FQDN* [ `,` *埠*]

其中：

* *FQDN*是端點的完整功能變數名稱。

* *Port*是端點的 TCP 埠。 根據預設， `1433` 會假設為。

> [!NOTE]
> 不支援指定網路端點的其他形式。
> 例如， `tcp:` 即使在以程式設計方式使用 SQL 用戶端程式庫時，還是可以省略前置詞。



::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援這項功能

::: zone-end
