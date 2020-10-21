---
title: 將資料匯出至 SQL-Azure 資料總管 |Microsoft Docs
description: 本文說明如何將資料匯出至 Azure 資料總管中的 SQL。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 61c65e2667356f2b2ee9e53c3dd1c210e84c72f8
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342802"
---
# <a name="export-data-to-sql"></a>將資料匯出至 SQL

將資料匯出至 SQL 可讓您執行查詢，並將其結果傳送至 SQL 資料庫中的資料表，例如 Azure SQL Database 服務所裝載的 SQL database。

**語法**

`.export`[ `async` ] `to` `sql` *SqlTableName* *SqlConnectionString* [ `with` `(` *PropertyName* `=` *PropertyValue* `,` ... `)` ] `<|`*查詢*

其中：
* *async*：命令以非同步模式執行 (選擇性) 。
* *SqlTableName* 插入資料的 SQL 資料庫資料表名稱。
  為了防止插入式攻擊，此名稱是受限制的。
* *SqlConnectionString* 是 `string` 遵循連接字串格式的常值， `ADO.NET` 並描述您連接的 SQL 端點和資料庫。 基於安全性理由，連接字串會受到限制。
* *PropertyName*， *PropertyValue* 是 (識別碼) 的成對名稱，以及 (字串常值) 的值。

內容：

|Name               |值           |描述|
|-------------------|-----------------|-----------|
|`firetriggers`     |`true` 或 `false`|如果 `true` 為，則指示目標系統引發 SQL 資料表上定義的 INSERT 觸發程式。 預設為 `false`。  (如需詳細資訊，請參閱 [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql) 和 [SqlClient SqlBulkCopy](/dotnet/api/system.data.sqlclient.sqlbulkcopy)) |
|`createifnotexists`|`true` 或 `false`|如果為 `true` ，將會建立目標 SQL 資料表（如果不存在的話）; `primarykey` 在此情況下，必須提供屬性以指出作為主鍵的結果資料行。 預設為 `false`。|
|`primarykey`       |                 |如果 `createifnotexists` 為 `true` ，則表示結果中的資料行名稱，如果是由這個命令所建立，則會當做 SQL 資料表的主鍵使用。|
|`persistDetails`   |`bool`           |指出命令應該保存其結果 (請參閱 `async` 旗標) 。 `true`在非同步執行中預設為，但如果呼叫端不需要) 的結果，則可以關閉。 預設為 `false` 同步執行，但可以開啟。 |
|`token`            |`string`         |Kusto 將轉送至 SQL 端點以進行驗證的 AAD 存取權杖。 設定時，SQL 連接字串不應包含驗證資訊 `Authentication` ，例如、 `User ID` 或 `Password` 。|

## <a name="limitations-and-restrictions"></a>限制事項

將資料匯出至 SQL database 時，有一些限制和限制：

1. Kusto 是一項雲端服務，因此連接字串必須指向可從雲端存取的資料庫。  (特別是，因為無法從公用雲端存取，所以無法匯出至內部部署資料庫。 ) 

2. 當呼叫主體是 Azure Active Directory 主體 (或) 時，Kusto 支援 Active Directory 整合式驗證 `aaduser=` `aadapp=` 。
   此外，Kusto 也支援提供 SQL database 的認證做為連接字串的一部分。 不支援其他驗證方法。 呈現給 SQL database 的身分識別一律會從命令呼叫端 emanates，而不是 Kusto 服務身分識別本身。

3. 如果 SQL 資料庫中的目標資料表存在，就必須符合查詢結果架構。 請注意，在某些情況下 (例如 Azure SQL Database) 這表示該資料表有一個資料行標示為識別資料行。

4. 匯出大量資料可能需要很長的時間。 建議您在大量匯入期間設定目標 SQL 資料表，以進行最基本的記錄。
   請參閱 [SQL Server 資料庫引擎 > ... > 資料庫功能 > 大量匯入和匯出資料](/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import)。

5. 資料匯出是使用 SQL 大量複製執行，並不會在目標 SQL 資料庫上提供交易式保證。 如需詳細資訊，請參閱 [交易和大量複製作業](/dotnet/framework/data/adonet/sql/transaction-and-bulk-copy-operations) 。

6. SQL 資料表名稱受限於包含字母、數位、空格、底線 (`_`) 、點 (`.`) 和連字號 () 的名稱 `-` 。

7. SQL 連接字串的限制如下： `Persist Security Info` 明確設定為 `false` 、 `Encrypt` 設定為 `true` ，且 `Trust Server Certificate` 設定為 `false` 。

8. 建立新的 SQL 資料表時，可以指定資料行的 primary key 屬性。 如果資料行的類型為 `string` ，則 SQL 可能會拒絕建立資料表，因為主鍵資料行有其他限制。 解決方法是在匯出資料之前，先在 SQL 中手動建立資料表。 這項限制的原因是 SQL 中的主鍵資料行不能有無限的大小，但 Kusto 資料表資料行沒有宣告的大小限制。

## <a name="azure-db-aad-integrated-authentication-documentation"></a>Azure 資料庫 AAD 整合式驗證檔

* [使用 Azure Active Directory 驗證進行 SQL Database 的驗證](/azure/sql-database/sql-database-aad-authentication)
* [適用于 Azure SQL DB 和 SQL DW tools Azure AD 驗證延伸模組] (https://azure.microsoft.com/blog/azure-ad-authentication-extensions-for-azure-sql-db-and-sql-dw-tools/)

**範例** 

在此範例中，Kusto 會執行查詢，然後將查詢所產生的第一個記錄集匯出至 `MySqlTable` `MyDatabase` server 資料庫中的資料表 `myserver` 。

```kusto 
.export async to sql MySqlTable
    h@"Server=tcp:myserver.database.windows.net,1433;Database=MyDatabase;Authentication=Active Directory Integrated;Connection Timeout=30;"
    <| print Id="d3b68d12-cbd3-428b-807f-2c740f561989", Name="YSO4", DateOfBirth=datetime(2017-10-15)
```

在此範例中，Kusto 會執行查詢，然後將查詢所產生的第一個記錄集匯出至 `MySqlTable` `MyDatabase` server 資料庫中的資料表 `myserver` 。
如果目標資料表不存在於目標資料庫中，則會加以建立。

```kusto 
.export async to sql ['dbo.MySqlTable']
    h@"Server=tcp:myserver.database.windows.net,1433;Database=MyDatabase;Authentication=Active Directory Integrated;Connection Timeout=30;"
    with (createifnotexists="true", primarykey="Id")
    <| print Message = "Hello World!", Timestamp = now(), Id=12345678
```