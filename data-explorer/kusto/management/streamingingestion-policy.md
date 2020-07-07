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
ms.openlocfilehash: 6f7efe2916bc5c5344bf4a6c4fa4a60bfaa167b7
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967344"
---
# <a name="streaming-ingestion-policy-command"></a>串流內嵌原則命令

您可以在資料表上設定串流內嵌原則，以允許在此資料表中內嵌串流。 您也可以在資料庫層級設定原則，以便將相同的設定套用到目前和未來的資料表。

如需詳細資訊，請參閱[串流](../../ingest-data-streaming.md)內嵌。 若要深入瞭解串流內嵌原則，請參閱[串流內嵌原則](streamingingestionpolicy.md)。

## <a name="display-the-policy"></a>顯示原則

此 `.show policy streamingingestion` 命令會顯示資料庫或資料表的串流內嵌原則。
 
**語法**

`.show``{database|table}` &lt; 實體 &gt; 名稱 `policy``streamingingestion`

**傳回**

此命令會傳回具有下列資料行的資料表：

|資料行    |類型    |描述
|---|---|---
|PolicyName|`string`|原則名稱-StreamingIngestionPolicy
|EntityName|`string`|資料庫或資料表名稱
|原則    |`string`|[串流內嵌原則物件](#streaming-ingestion-policy-object)

**範例**

```kusto
.show database DB1 policy streamingingestion

.show table T1 policy streamingingestion
```

|PolicyName|EntityName|原則|ChildEntities|EntityType|
|---|---|---|---|---|
|StreamingIngestionPolicy|DB1|{"IsEnabled"： true，"HintAllocatedRate"： null}

## <a name="change-the-policy"></a>變更原則

這 `.alter[-merge] policy streamingingestion` 系列的命令會提供修改資料庫或資料表之資料流程內嵌原則的方法。

**語法**

* `.alter``{database|table}` &lt; &gt; 實體 `policy` 名稱 `streamingingestion``[enable|disable]`

* `.alter``{database|table}` &lt; 機構名稱 &gt; `policy` `streamingingestion` &lt; [串流內嵌原則物件](#streaming-ingestion-policy-object)&gt;

* `.alter-merge``{database|table}` &lt; 機構名稱 &gt; `policy` `streamingingestion` &lt; [串流內嵌原則物件](#streaming-ingestion-policy-object)&gt;

> [!Note]
>
> * 允許變更串流內嵌的已啟用/已停用狀態，而不需要修改原則的其他屬性或將屬性設定為預設值（如果尚未在實體上定義原則）。
>
> * 允許取代實體上的整個串流內嵌原則。 [串流內嵌原則物件](#streaming-ingestion-policy-object)必須包含所有必要的屬性。
>
> * 只允許取代實體上的資料流程內嵌原則的指定屬性。 [串流內嵌原則物件](#streaming-ingestion-policy-object)可以包含部分或無強制屬性。

**傳回**

此命令會修改資料表或資料庫 `streamingingestion` 原則物件，然後傳回對應[ `.show policy` `streamingingestion` ](#display-the-policy)命令的輸出。

**範例**

```kusto
.alter database DB1 policy streamingingestion enable

.alter table T1 policy streamingingestion disable

.alter database DB1 policy streamingingestion '{"IsEnabled": true, "HintAllocatedRate": 2.1}'

.alter table T1 streamingingestion '{"IsEnabled": true}'

.alter-merge database DB1 policy streamingingestion '{"IsEnabled": false}'

.alter-merge table T1 policy streamingingestion '{"HintAllocatedRate": 1.5}'
```

## <a name="delete-the-policy"></a>刪除原則

此 `.delete policy streamingingestion` 命令會從資料庫或資料表中刪除 streamingingestion 原則。

**語法**

`.delete``{database|table}` &lt; 實體 &gt; 名稱 `policy``streamingingestion`

**傳回**

此命令會刪除資料表或資料庫 streamingingestion 原則物件，然後傳回對應之的輸出[。顯示原則 streamingingestion](#display-the-policy)命令。

**範例**

```kusto
.delete database DB1 policy streamingingestion

.delete table T1 policy streamingingestion
```

### <a name="streaming-ingestion-policy-object"></a>串流內嵌原則物件

在管理命令的輸入和輸出中，串流內嵌原則物件是 JSON 格式的字串，其中包含下列屬性。

|屬性|類型|描述|必要/選用
|---|---|---|---
|IsEnabled|`bool`|已針對實體啟用串流內嵌| 必要
|HintAllocatedRate|`double`|資料會輸入的預估速率（Gb/小時）|選擇性
