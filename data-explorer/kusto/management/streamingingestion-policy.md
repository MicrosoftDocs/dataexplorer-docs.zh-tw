---
title: Kusto 串流內嵌原則管理-Azure 資料總管
description: 本文說明 Azure 資料總管中的串流內嵌原則管理。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 1c3ce0c0d383d07375333b08de336503d1578b1a
ms.sourcegitcommit: fd3bf300811243fc6ae47a309e24027d50f67d7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83381990"
---
# <a name="streaming-ingestion-policy-management"></a>串流處理內嵌原則管理

串流內嵌原則可以附加到資料庫或資料表，以允許將串流內嵌到那些位置。 此原則也會定義用於串流內嵌的資料列存放區。

如需串流內嵌的詳細資訊，請參閱[串流內嵌（預覽）](../../ingest-data-streaming.md)。 若要深入瞭解串流內嵌原則，請參閱[串流內嵌原則](streamingingestionpolicy.md)。

## <a name="show-policy-streamingingestion"></a>。顯示原則 streamingingestion

此 `.show policy streamingingestion` 命令會顯示資料庫或資料表的串流內嵌原則。

**語法**

`.show``database` `policy` `streamingingestion` 
 MyDatabase `.show``table`MyTable `policy``streamingingestion`

**傳回**

此命令會傳回具有下列資料行的資料表：

|資料行    |類型    |說明
|---|---|---
|PolicyName|`string`|原則名稱-StreamingIngestionPolicy
|EntityName|`string`|資料庫或資料表名稱
|原則    |`string`|JSON 物件，定義串流內嵌原則，格式為串流內嵌[原則物件](#streaming-ingestion-policy-object)

**範例**

```kusto
.show database DB1 policy streamingingestion 
.show table T1 policy streamingingestion 
```

|PolicyName|EntityName|原則|ChildEntities|EntityType|
|---|---|---|---|---|
|StreamingIngestionPolicy|DB1|{"NumberOfRowStores"： 4}

### <a name="streaming-ingestion-policy-object"></a>串流內嵌原則物件

|屬性  |類型    |說明                                                       |
|----------|--------|------------------------------------------------------------------|
|NumberOfRowStores |`int`  |指派給實體的資料列存放區數目|
|SealIntervalLimit|`TimeSpan?`|選擇性限制資料表上密封作業之間的間隔。 有效範圍介於1到24小時之間。 預設值：24 小時。|
|SealThresholdBytes|`int?`|資料表上單一密封作業所需的資料量選擇性限制。 值的有效範圍介於10到 200 Mb 之間。 預設值： 200 Mb。|

## <a name="alter-policy-streamingingestion"></a>。 alter policy streamingingestion

`.alter policy streamingingestion`命令會設定資料庫或資料表的 streamingingestion 原則。

**語法**

* `.alter``database` `policy` MyDatabase `streamingingestion`*StreamingIngestionPolicyObject*

* `.alter``database` `policy` MyDatabase `streamingingestion``enable`

* `.alter``database` `policy` MyDatabase `streamingingestion``disable`

* `.alter``table` `policy` MyTable `streamingingestion`*StreamingIngestionPolicyObject*

* `.alter``table` `policy` MyTable `streamingingestion``enable`

* `.alter``table` `policy` MyTable `streamingingestion``disable`

**注意事項**

1. *StreamingIngestionPolicyObject*是已定義串流內嵌原則物件的 JSON 物件。

2. `enable`-如果原則未定義或已使用 0 rowstores 定義，則將串流內嵌原則設定為使用 4 rowstores，否則命令不會執行任何動作。

3. `disable`-如果已使用正 rowstores 定義原則，則將串流內嵌原則設定為，否則為 rowstores，否則命令不會執行任何動作。

**範例**

```kusto
.alter database MyDatabase policy streamingingestion '{  "NumberOfRowStores": 4}'

.alter table MyTable policy streamingingestion '{  "NumberOfRowStores": 4}'
```

## <a name="delete-policy-streamingingestion"></a>。刪除原則 streamingingestion

此 `.delete policy streamingingestion` 命令會從資料庫或資料表中刪除 streamingingestion 原則。

**語法** 

`.delete``database`MyDatabase `policy``streamingingestion`

`.delete``table`MyTable `policy``streamingingestion`

**傳回**

此命令會刪除資料表或資料庫 streamingingestion 原則物件，然後傳回對應之的輸出[。顯示原則 streamingingestion](#show-policy-streamingingestion)命令。

**範例**

```kusto
.delete database MyDatabase policy streamingingestion 
```