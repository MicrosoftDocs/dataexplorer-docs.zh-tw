---
title: sql_request外掛程式 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的sql_request外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: f0c0837c6bb8e4dcd3cf2e28af18d02c19edb676
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766169"
---
# <a name="sql_request-plugin"></a>sql_request 外掛程式

::: zone pivot="azuredataexplorer"

  `evaluate``sql_request``,``(`連接`,`串 SqlQuery = Sql 參數 [ 選項 ] * * `,` * * * * * *`)`

該`sql_request`外掛程式向 SQL Server 網路終結點發送 SQL 查詢,並返回結果中的第一個行集。

**引數**

* *連接字串*:`string`指示指向 SQL Server 網路終結點的連接字串的文字。 有關有效的身份驗證方法以及如何指定網路終結點,請參閱下文。

* *SqlQuery*`string`: 指示要針對 SQL 終結點執行的查詢的文本。 必須返回一個或多個行集,但只有第一個行集可用於 Kusto 查詢的其餘部分。

* *Sql參數*:一種類型`dynamic`的 常量值,用於保存鍵值對作為參數與查詢一起傳遞。 選擇性。
  
* *選項*:`dynamic`類型 常量值,將更高級的設置作為鍵值對。 目前只能`token`設置,以傳遞轉接到 SQL 終結點進行身份驗證的調用方提供的 AAD 訪問權杖。 選擇性。

**範例**

下面的示例將 SQL 查詢發送到 Azure SQL`[dbo].[Table]`資料庫資料庫,從檢索所有記錄,然後在 Kusto 端處理結果。 身份驗證重用調用使用者的 AAD 權杖。

注意:不應將此示例視為以這種方式篩選/專案數據的建議。 通常最好構造 SQL 查詢以返回盡可能小的數據集,因為目前 Kusto 優化器不會嘗試最佳化 Kusto 和 SQL 之間的查詢。

```kusto
evaluate sql_request(
  'Server=tcp:contoso.database.windows.net,1433;'
    'Authentication="Active Directory Integrated";'
    'Initial Catalog=Fabrikam;',
  'select * from [dbo].[Table]')
| where Id > 0
| project Name
```

下面的示例與前一個示例相同,只不過 SQL 身份驗證是通過使用者名/密碼完成的。 請注意,為了保密,我們在此處使用模糊字串。

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

**驗證**

sql_request外掛程式支援對 SQL Server 終結點的三種身份驗證方法:

* **AAD 整合身份驗證**(`Authentication="Active Directory Integrated"`): 這是首選方法,使用者或應用程式透過 AAD 對 Kusto 進行身份驗證,然後使用相同的權杖存取 SQL Server 網路終結點。

* **使用者名/密碼身份驗證**`User ID=...; Password=...;`( ): 當無法執行 AAD 整合身份驗證時,將提供對此方法的支援。 盡可能避免使用此方法,因為機密信息是通過 Kusto 發送的。

* **AAD 存取權杖**(`dynamic({'token': h"eyJ0..."})`): 使用此身份驗證方法,存取權杖由呼叫者生成並由 Kusto 轉發到 SQL 終結點。 連接字串不應包括認證資訊,如`Authentication`、`User ID`或`Password`。 相反,訪問權杖在sql_request外掛程式`token`的`Options`參數中 作為屬性傳遞。
     
> [!WARNING]
> 連接字串和查詢,包括機密資訊或應保護的資訊,應混淆,以便從任何 Kusto 跟蹤中省略它們。
> 關於詳細資訊,請參考[模糊字串文字](scalar-data-types/string.md#obfuscated-string-literals)。

**加密與伺服器驗證**

出於安全原因,連線到 SQL Server 網路終結點時,將強制使用以下連接屬性:

* `Encrypt`設置為`true`無條件。
* `TrustServerCertificate`設置為`false`無條件。

因此,SQL 伺服器必須配置有效的 SSL/TLS 伺服器證書。

**指定網路終結點**

必須將 SQL 網路終結點指定為連接字串的一部分。
適當的語法為：

`Server``=` *FQDN* FQDN • 連接埠 | `tcp:` `,` * *

其中：

* *FQDN*是終結點的完全限定功能變數名稱。

* *埠*是端點的 TCP 埠。 預設情況下,`1433`假定為。

> [!NOTE]
> 不支援指定網路終結點的其他形式的指定。
> 例如,即使以程式設計方式使用 SQL`tcp:`用戶端庫時可以省略前綴,也可以省略前綴。



::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援此功能

::: zone-end
