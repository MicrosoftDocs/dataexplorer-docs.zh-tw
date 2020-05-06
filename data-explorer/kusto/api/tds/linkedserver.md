---
title: 從 SQL server Kusto 為連結伺服器-Azure 資料總管
description: 本文說明如何從 Azure 資料總管中的 SQL server Kusto 為連結的伺服器。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 103d39f7fdd10f0375e4a530d51cd17f7cdcec51
ms.sourcegitcommit: 061eac135a123174c85fe1afca4d4208c044c678
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82799589"
---
# <a name="kusto-as-a-linked-server-from-the-sql-server"></a>從 SQL server Kusto 為連結的伺服器

內部部署的 SQL server 可讓您附加連結的伺服器，並可讓您建立查詢，以便從 SQL server 和連結的伺服器聯結資料。

您可以透過 ODBC 連接使用 Kusto 做為連結的伺服器。
SQL Server 內部部署服務必須使用 active directory 帳戶（而非預設的服務帳戶），讓它使用 Azure Active Directory （Azure AD）連接到 Azure 資料總管。

1. 安裝適用于 SQL Server 2017 的最新 ODBC 驅動程式（它也隨附于 SSMS 18）：https://www.microsoft.com/download/details.aspx?id=56567
1. 針對特定的 Azure 資料總管叢集和資料庫，準備 ODBC 驅動程式的無 DSN 連接字串： `Driver={ODBC Driver 17 for SQL Server};Server=<cluster>.kusto.windows.net;Database=<database>;Authentication=ActiveDirectoryIntegrated;Language=any@MaxStringSize:4000`。 已新增 [語言] 選項來微調 Azure 資料總管以將字串編碼為 NVARCHAR （4000）。 如需有關此解決方法的詳細資訊，請參閱[ODBC](./clients.md#odbc)。
1. 使用紅色箭號所指向的設定來建立連結的伺服器。

:::image type="content" source="../images/linkedserverconnection.png" alt-text="連結的伺服器連接":::

1. 使用紅色箭號所指向的設定來定義安全性。 

:::image type="content" source="../images/linkedserverlogin.png" alt-text="連結的伺服器登入":::

若要從 Kusto 查詢資料：

```sql
SELECT * FROM OpenQuery(LINKEDSERVER, 'SELECT * FROM <KustoStoredFunction>[(<Parameters>)]')
```

> [!NOTE]
> 1. 使用 Kusto 的[預存](../../query/schema-entities/stored-functions.md)函式，從 Azure 資料總管解壓縮資料。 預存函數可以包含從 Kusto 有效率的查詢所需的所有邏輯，以原生[KQL](../../query/index.md)語言撰寫，並由指定的參數值控制。 呼叫 Kusto 預存函數的 t-sql 語法與呼叫 SQL 表格式函數相同。
> 1. SQL server 不支援在自己的查詢內從連結的伺服器呼叫遠端表格式函數。 這項限制的因應措施是`OpenQuery`使用在連結的伺服器上執行遠端查詢。 如此一來，就不會在 SQL server 目錄上呼叫表格式函數，而是在連結伺服器上執行的查詢中。 外部 T-SQL 查詢可以用來聯結 SQL server 上的資料，以及透過 Kusto 預存函式傳回的資料`OpenQuery`。
