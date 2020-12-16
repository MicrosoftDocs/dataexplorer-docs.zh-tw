---
title: 。顯示資料庫架構-Azure 資料總管 |Microsoft Docs
description: 本文說明如何在 Azure 資料總管中顯示資料庫架構。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4f3639aeb6e401aa37703bbef929af2275960a91
ms.sourcegitcommit: 35236fefb52978ce9a09bc36affd5321acb039a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97513984"
---
# <a name="show-database-schema-commands"></a>。顯示資料庫架構命令

下列命令會將資料庫架構顯示為數據表、JSON 物件或 CSL 腳本。

## <a name="show-databases-schema"></a>。顯示資料庫架構

**語法**

`.show``database` *DatabaseName* `schema` [ `details` ] [ `if_later_than` *"Version"*] 

`.show``databases` `(` *DatabaseName1* `,` ... `)` `schema``details` 
 
`.show``databases` `(` *DatabaseName1* if_later_than *"Version"* `,` ... `)` `schema``details`

**傳回**

傳回所選資料庫結構的一般清單，以及其在單一資料表或 JSON 物件中的所有資料表和資料行。
與版本搭配使用時，只有在其版本比提供的版本還新時，才會傳回資料庫。

> [!NOTE]
> 版本只應以 "vMM.mm" 格式提供。 MM 代表主要版本，而 mm 代表次要版本。

**範例** 
 
資料庫 ' TestDB ' 有一個稱為「事件」的資料表。

```kusto
.show database TestDB schema 
```

**輸出**

|DatabaseName|TableName|ColumnName|ColumnType|IsDefaultTable|IsDefaultColumn|PrettyName|版本
|---|---|---|---|---|---|---|--- 
|TestDB||||False|False||v1。1       
|TestDB|事件|||是|否||       
|TestDB|事件| 名稱|System.String|是|否||     
|TestDB|事件| StartTime|  System.DateTime|是|否||    
|TestDB|事件| EndTime|    System.DateTime|是|否||        
|TestDB|事件| City|   System.String|是| 否||     
|TestDB|事件| SessionId|  System.Int32|True|  True|| 

**範例** 

在下列範例中，只有當資料庫的版本比提供的版本還新時，才會傳回資料庫。
 
```kusto
.show database TestDB schema if_later_than "v1.0" 
```

**輸出**

|DatabaseName|TableName|ColumnName|ColumnType|IsDefaultTable|IsDefaultColumn|PrettyName|版本
|---|---|---|---|---|---|---|--- 
|TestDB||||False|False||v1。1       
|TestDB|事件|||是|否||       
|TestDB|事件| 名稱|System.String|是|否||     
|TestDB|事件| StartTime|  System.DateTime|是|否||    
|TestDB|事件| EndTime|    System.DateTime|是|否||        
|TestDB|事件| City|   System.String|是| 否||     
|TestDB|事件| SessionId|  System.Int32|True|  True||  

因為提供的版本低於目前的資料庫版本，所以會傳回 ' TestDB ' 架構。 提供相等或更高的版本會產生空的結果。

## <a name="show-database-schema-as-json"></a>。將資料庫架構顯示為 json

**語法**

`.show``database` *DatabaseName* `schema` [ `if_later_than` *"Version"*] `as``json`
 
`.show``databases` `(` *DatabaseName1* `,` ... `)` `schema` `as``json`
 
`.show``databases` `(` *DatabaseName1* if_later_than *"Version"* `,` `)` `schema` ... `as``json`

**傳回**

傳回所選資料庫結構的一般清單，並將其所有資料表和資料行做為 JSON 物件。
與版本搭配使用時，只有在其版本比提供的版本還新時，才會傳回資料庫。

**範例** 
 
```kusto
.show database TestDB schema as json
```

**輸出**

```json
"{""Databases"":{""TestDB"":{""Name"":""TestDB"",""Tables"":{""Events"":{""Name"":""Events"",""DefaultColumn"":null,""OrderedColumns"":[{""Name"":""Name"",""Type"":""System.String""},{""Name"":""StartTime"",""Type"":""System.DateTime""},{""Name"":""EndTime"",""Type"":""System.DateTime""},{""Name"":""City"",""Type"":""System.String""},{""Name"":""SessionId"",""Type"":""System.Int32""}]}},""PrettyName"":null,""MajorVersion"":1,""MinorVersion"":1,""Functions"":{}}}}"
```

## <a name="show-database-schema-as-csl-script"></a>。將資料庫架構顯示為 csl 腳本

產生 CSL 腳本，其中包含所有必要的命令，以建立指定 (或目前) 資料庫架構的複本。

**語法**

`.show``database`  `schema` DatabaseName `as``csl` `script` [ `with(` *選項* `)` ]

**引數**

下列 *選項* 都是選擇性的：

* `IncludeEncodingPolicies`： (`true`  |  `false`) -如果 `true` ，會包含資料庫/資料表/資料行層級的編碼原則。 預設為 `false`。 
* `IncludeSecuritySettings`： (`true`  |  `false`) -預設為 `false` 。 如果 `true` 為，則會包含下列選項：
  * 資料庫/資料表層級的授權主體。
  * 資料表層級的資料列層級安全性原則。
  * 資料表層級的限制視圖存取原則。
* `IncludeIngestionMappings`： (`true`  |  `false`) -如果 `true` ，會包含資料表層級的內嵌對應。 預設為 `false`。 

**傳回**

以字串形式傳回的腳本將包含：

* 用來在資料庫中建立所有資料表的命令。
* 用來設定所有資料庫/資料表/資料行原則以符合原始原則的命令。
* 用來建立或改變資料庫中所有使用者定義函數的命令。

**範例** 
 
```kusto
.show database TestDB schema as csl script

.show database TestDB schema as csl script with(IncludeSecuritySettings = true)
```
