---
title: 函式庫斯托作為從 SQL 伺服器連結伺服器 - Azure 資料資源管理員 |微軟文件
description: 本文將 Kusto 描述為 Azure 資料資源管理員中的 SQL 伺服器的連結伺服器。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 77db975f2bd7c5c1d747902248070664cd020e39
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523455"
---
# <a name="kusto-as-linked-server-from-sql-server"></a>庫斯托作為從 SQL 伺服器連結伺服器

本地 SQL 伺服器允許附加連結的伺服器。 此功能允許創建從 SQL 伺服器和連結伺服器聯接數據的查詢。

可以通過ODBC連接將Kusto用作連結伺服器。

1. SQL Server(本地)服務需要使用允許使用 AAD 連接到 Kusto 的活動目錄帳戶(而不是預設服務帳戶)。
2. 為 SQL Server 2017 安裝最新的 ODBC 驅動程式(它還附帶 SSMS 18):https://www.microsoft.com/download/details.aspx?id=56567
3. 為特定 Kusto 叢集與資料庫為 ODBC 驅動程式準備`Driver={ODBC Driver 17 for SQL Server};Server=<cluster>.kusto.windows.net;Database=<database>;Authentication=ActiveDirectoryIntegrated;Language=any@MaxStringSize:4000`DSN 較少的 連接字串: . 添加語言選項以將 Kusto 編碼為 NVARCHAR(4000)。 在[ODBC](./clients.md#odbc)上查看有關此解決方法的更多詳細資訊。
4. 建立已定義的以下 3 個設定的連結伺服器![:alt 文字](../images/linkedserverconnection.png "連結的伺服器連線")
5. 需要使用這個設定定義 「安全」選項卡![:alt 文字](../images/linkedserverlogin.png "連結伺服器登入")

現在,可以從 Kusto 查詢數據,如下所示:
```sql
SELECT * FROM OpenQuery(LINKEDSERVER, 'SELECT * FROM <KustoStoredFunction>[(<Parameters>)]')
```

注意：
1. 建議使用 Kusto[儲存的函數](../../query/schema-entities/stored-functions.md)從 Kusto 中提取數據。 存儲函數可以包括從 Kusto 進行有效查詢所需的所有邏輯,該邏輯以本機[KQL](../../query/index.md)語言編寫,並由指定的參數值控制。 用於呼叫 Kusto 儲存函數的 T-SQL 語法與調用 SQL 表面函數相同。
2. SQL 伺服器不支援在自己的查詢中從連結伺服器調用遠端表格函數。 此限制的解決方法是用於`OpenQuery`在連結的伺服器上執行遠程查詢。 這樣,表位函數不是直接在 SQL 伺服器上調用的,而是在連結伺服器上執行的查詢中調用的。 外部 T-SQL 查詢可用於在 SQL 伺服器上`OpenQuery`的數據和通過 從 Kusto 儲存函數傳回的數據之間聯接。