---
title: 將資料匯出到 SQL - Azure 資料資源管理員 |微軟文件
description: 本文介紹在 Azure 資料資源管理器中將數據匯出到 SQL。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7deeb3868800f00a828eb09cc605e4d7e5227032
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521619"
---
# <a name="export-data-to-sql"></a>匯出資料到 SQL

將資料匯出到 SQL 允許您執行查詢,並將其結果發送到 SQL 資料庫中的表,例如由 Azure SQL 資料庫服務託管的 SQL 資料庫。

**語法**

`.export`[`async` `to` `with` `(` `=`*PropertyValue*`,`*PropertyName* *SqlTableName* ] SqlTable 名稱*SqlConnectionString* = 屬性名稱 屬性值 ... `sql``)`• `<|` *查詢*

其中：
* *非同步*:命令在非同步模式下執行(可選)。
* *SqlTable 名稱*插入資料的 SQL 資料庫表名稱。
  為了防止注入攻擊,此名稱受到限制。
* *SqlConnectionString*`string`是`ADO.NET`遵循 連接字串格式並描述您連接到的 SQL 終結點和資料庫的文本。 出於安全原因,連接字串受到限制。
* *屬性名稱**屬性值*是名稱(標識符)和值(字串文本)的對。

屬性：

|名稱               |值           |描述|
|-------------------|-----------------|-----------|
|`firetriggers`     |`true` 或 `false`|如果`true`指示目標系統觸發 SQL 表上定義的 INSERT 觸發器。 預設值為 `false`。 (有關詳細資訊,請參閱[BULK INSERT](https://msdn.microsoft.com/library/ms188365.aspx)和[系統.Data.SqlClient.SqlBulkCopy](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy(v=vs.110).aspx))|
|`createifnotexists`|`true` 或 `false`|如果`true`,將創建目標 SQL 表(如果不存在);在這種情況下`primarykey`,必須提供該屬性來指示作為主鍵的結果列。 預設值為 `false`。|
|`primarykey`       |                 |如果`createifnotexists``true`是 ,則指示結果中的列的名稱,如果該列是由此命令創建的,則該列將用作 SQL 表的主鍵。|
|`persistDetails`   |`bool`           |指示命令應保留其結果(請參閱`async`標誌)。 在非同步`true`中運行預設值,但如果調用方不需要結果,則可以關閉。 在同步執行`false`中預設為,但可以打開。 |
|`token`            |`string`         |Kusto 將轉發到 SQL 終結點進行身份驗證的 AAD 訪問權杖。 設定時,SQL 連接字串不應包含身份驗證資訊,如`Authentication``User ID`。`Password`或 。 或 。|

## <a name="limitations-and-restrictions"></a>限制事項

將資料匯出到 SQL 資料庫時,存在許多限制和限制:

1. Kusto 是一個雲服務,因此連接字串必須指向可從雲端存取的資料庫。 (特別是,由於公共雲無法訪問本地資料庫,因此無法匯出到本地資料庫。

2. 當調用主體是 Azure 活動目錄`aaduser=`主體`aadapp=`( 或 ) 時,Kusto 支援活動目錄集成身份驗證。
   或者,Kusto 還支援作為連接字串的一部分提供 SQL 資料庫的認證。 不支援其他身份驗證方法。 顯示到 SQL 資料庫的標識始終來自命令調用方,而不是 Kusto 服務標識本身。

3. 如果 SQL 資料庫中的目標表存在,則必須與查詢結果架構匹配。 請注意,在某些情況下(如 Azure SQL 資料庫),這意味著表有一列標記為標識列。

4. 匯出大量數據可能需要很長時間。 建議在批量導入期間設置目標 SQL 表以最少的日誌記錄。
   請參考[SQL Server 資料庫引擎>... >数据库功能>批量匯入與匯出資料](https://msdn.microsoft.com/library/ms190422.aspx)。

5. 數據匯出使用 SQL 批量複製執行,並且不提供目標 SQL 資料庫的事務保證。 有關詳細資訊,請參閱[事務和批次複製操作](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/transaction-and-bulk-copy-operations)。

6. SQL 表名稱限制為由字母、數位、空格、下劃線`_`()、`.`點 (`-`) 和連字元 () 組成的名稱。

7. SQL 連接字串限制`Persist Security Info`如下: 顯示式`false`設定`Encrypt`為`true`,`Trust Server Certificate`設定為`false`, 並設定為 。

8. 創建新 SQL 表時,可以指定列上的主鍵屬性。 如果列的類型`string`,則 SQL 可能會由於主鍵列上的其他限制而拒絕創建表。 解決方法是在匯出數據之前在 SQL 中手動創建表。 此限制的原因是 SQL 中的主鍵列的大小不能不受限制,但 Kusto 表列沒有聲明的大小限制。

## <a name="azure-db-aad-integrated-authentication-documentation"></a>Azure DB AAD 整合身份驗證文件

* [使用 Azure 活動目錄認證對 SQL 資料庫進行身份驗證](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)
* [Azure SQL DB 與 SQL DW 工具的 Azure AD 認證擴充](https://azure.microsoft.com/blog/azure-ad-authentication-extensions-for-azure-sql-db-and-sql-dw-tools/)

**範例** 

在此示例中,Kusto 運行查詢,然後將查詢生成的第一個記錄集匯出到`MySqlTable``MyDatabase``myserver`伺服器 資料庫中的表。

```kusto 
.export async to sql MySqlTable
    h@"Server=tcp:myserver.database.windows.net,1433;Database=MyDatabase;Authentication=Active Directory Integrated;Connection Timeout=30;"
    <| print Id="d3b68d12-cbd3-428b-807f-2c740f561989", Name="YSO4", DateOfBirth=datetime(2017-10-15)
```

在此示例中,Kusto 運行查詢,然後將查詢生成的第一個記錄集匯出到`MySqlTable``MyDatabase``myserver`伺服器 資料庫中的表。
如果目標表在目標資料庫中不存在,則創建目標表。

```kusto 
.export async to sql ['dbo.MySqlTable']
    h@"Server=tcp:myserver.database.windows.net,1433;Database=MyDatabase;Authentication=Active Directory Integrated;Connection Timeout=30;"
    with (createifnotexists="true", primarykey="Id")
    <| print Message = "Hello World!", Timestamp = now(), Id=12345678
```