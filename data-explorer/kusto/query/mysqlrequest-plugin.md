---
title: mysql_request 外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中 mysql_request 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: e20e266e6fbae55c308cf13b7601277b8b0f30b2
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320735"
---
# <a name="mysql_request-plugin-preview"></a>mysql_request 外掛程式 (預覽) 

::: zone pivot="azuredataexplorer"

`mysql_request`外掛程式會將 SQL 查詢傳送至 MySQL 伺服器網路端點，並傳回結果中的第一個資料列集。 查詢可能會傳回一個以上的資料列集，但 Kusto 查詢的其餘部分只會提供第一個資料列集。

> [!IMPORTANT]
> `mysql_request`外掛程式處於預覽模式，預設為停用。
> 若要啟用外掛程式，請執行[ `.enable plugin mysql_request` 命令](../management/enable-plugin.md)。 若要查看已啟用哪些外掛程式，請使用[ `.show plugin` 管理命令](../management/show-plugins.md)。

## <a name="syntax"></a>語法

`evaluate``mysql_request` `(` *ConnectionString* `,` *SqlQuery* [ `,` *SqlParameters*]`)`

## <a name="arguments"></a>引數

名稱 | 類型 | 描述 | 必要/選用 |
---|---|---|---
| *ConnectionString* | `string` literal | 指出指向 MySQL 伺服器網路端點的連接字串。 請參閱 [驗證](#username-and-password-authentication) 以及如何指定 [網路端點](#specify-the-network-endpoint)。 | 必要 |
| *SqlQuery* | `string` literal | 表示要對 SQL 端點執行的查詢。 必須傳回一或多個資料列集，但 Kusto 查詢的其餘部分只能使用第一個資料列集。 | 必要|
| *SqlParameters* | 類型的常數值 `dynamic` | 保存索引鍵/值組，以傳遞做為參數和查詢。 | 選擇性 |

## <a name="set-callout-policy"></a>設定標注原則

外掛程式會在 MySql 資料庫中製作標注。 確定叢集的注標 [原則](../management/calloutpolicy.md)可讓您對目標 MySqlDbUri 進行類型的呼叫 `mysql` 。 *MySqlDbUri*

下列範例顯示如何定義適用于 MySQL 資料庫的標注原則。 建議您將注標原則限制在 () 的特定 `my_endpoint1` 端點 `my_endpoint2` 。

```kusto
[
  {
    "CalloutType": "mysql",
    "CalloutUriRegex": "my_endpoint1\\.mysql\\.database\\.azure\\.com",
    "CanCall": true
  },
  {
    "CalloutType": "mysql",
    "CalloutUriRegex": "my_endpoint2\\.mysql\\.database\\.azure\\.com",
    "CanCall": true
  }
]
```

下列範例顯示 CalloutType 的 alter callout 原則命令 `mysql` **：

```kusto
.alter cluster policy callout @'[{"CalloutType": "mysql", "CalloutUriRegex": "\\.mysql\\.database\\.azure\\.com", "CanCall": true}]'
```

## <a name="username-and-password-authentication"></a>使用者名稱和密碼驗證

Mysql_request 外掛程式僅支援對 MySQL 伺服器端點進行使用者名稱和密碼驗證，且不會與 Azure Active Directory 驗證整合。 

使用下列參數將使用者名稱和密碼提供為連接字串的一部分：

`User ID=...; Password=...;`
    
> [!WARNING]
> 機密或保護資訊應該從連接字串和查詢進行混淆，以便從任何 Kusto 追蹤中省略。 如需詳細資訊，請參閱 [模糊字串常](scalar-data-types/string.md#obfuscated-string-literals)值。

## <a name="encryption-and-server-validation"></a>加密和伺服器驗證

基於安全性理由， `SslMode` `Required` 當連接到 MySQL 伺服器網路端點時，會無條件地設定為。 因此，必須使用有效的 SSL/TLS 伺服器憑證來設定 SQL Server。

## <a name="specify-the-network-endpoint"></a>指定網路端點

將 SQL 網路端點指定為連接字串的一部分。

**語法**：

`Server``=` *FQDN* [ `Port` `=` *埠*]

其中：

* *FQDN* 是端點的完整功能變數名稱。
* *Port* 是端點的 TCP 埠。 預設 `3306` 會假設為。

## <a name="examples"></a>範例


### <a name="sql-query-to-azure-mysql-db"></a>Azure MySQL 資料庫的 SQL 查詢

下列範例會將 SQL 查詢傳送至 Azure MySQL DB 資料庫。 它會從取出所有記錄 `[dbo].[Table]` ，然後處理結果。

> [!NOTE]
> 此範例不應該做為建議，以這種方式篩選或投影資料。 由於 Kusto 優化器目前未嘗試優化 Kusto 與 SQL 之間的查詢，因此應將 SQL 查詢視為可傳回最小的資料集。

```kusto
evaluate sql_request(
    'Server=contoso.mysql.database.azure.com; Port = 3306;'
    'Database=Fabrikam;'
    h'UID=USERNAME;'
    h'Pwd=PASSWORD;', 
  'select * from [dbo].[Table]')
| where Id > 0
| project Name
```

### <a name="sql-authentication-with-username-and-password"></a>具有使用者名稱和密碼的 SQL 驗證

下列範例與上一個範例相同，但 SQL 驗證是以使用者名稱和密碼來完成。 為了機密性，我們使用模糊的字串。

```kusto
evaluate sql_request(
   'Server=contoso.mysql.database.azure.com; Port = 3306;'
    'Database=Fabrikam;'
    h'UID=USERNAME;'
    h'Pwd=PASSWORD;', 
  'select * from [dbo].[Table]')
| where Id > 0
| project Name
```

### <a name="sql-query-to-azure-sql-db-with-modifications"></a>Azure SQL DB 的 SQL 查詢與修改

下列範例會將 SQL 查詢傳送至 Azure SQL DB 資料庫，以從中取出所有記錄 `[dbo].[Table]` ，同時附加另一個資料 `datetime` 行，然後在 Kusto 端處理結果。
它會指定 `@param0` 要在 SQL 查詢中使用 () 的 sql 參數。

```kusto
evaluate mysql_request(
  'Server=contoso.mysql.database.azure.com; Port = 3306;'
    'Database=Fabrikam;'
    h'UID=USERNAME;'
    h'Pwd=PASSWORD;', 
  'select *, @param0 as dt from [dbo].[Table]',
  dynamic({'param0': datetime(2020-01-01 16:47:26.7423305)}))
| where Id > 0
| project Name
```

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援這項功能

::: zone-end
