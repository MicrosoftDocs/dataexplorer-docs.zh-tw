---
title: 流式處理原則管理 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的流式處理策略管理。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: b0b1a76e52688dcc88ca87023309f9c970b4c702
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519613"
---
# <a name="streaming-ingestion-policy-management"></a>串流式引入原則管理

流引入策略可以附加到資料庫或表,以允許流式傳輸引入這些位置。 該策略還定義用於流式引入的行存儲。

有關流式引入的詳細資訊,請參閱[流式引入(預覽)。](https://docs.microsoft.com/azure/data-explorer/ingest-data-streaming) 要瞭解有關串流引入策略的更多,請參閱[串流引入策略](streamingingestionpolicy.md)。

## <a name="show-policy-streamingingestion"></a>.顯示策略流式處理

該`.show policy streamingingestion`命令顯示資料庫或表的流式引入策略。

**語法**

`.show``database``policy`我的`streamingingestion`資料庫
我的表`.show``policy``table``streamingingestion`

**傳回**

此指令傳回有以下欄的表:

|資料行    |類型    |描述
|---|---|---
|PolicyName|`string`|原則名稱 - 串流化原則
|EntityName|`string`|資料庫或表格名稱
|原則    |`string`|定義串流引入策略的 JSON 物件格式化為[串流式引入策略物件](#streaming-ingestion-policy-object)

**範例**

```kusto
.show database DB1 policy streamingingestion 
.show table T1 policy streamingingestion 
```

|PolicyName|EntityName|原則|子實體|EntityType|
|---|---|---|---|---|
|串流化原則|DB1|[ "羅羅商店數量": 4 |

### <a name="streaming-ingestion-policy-object"></a>串流式引入策略物件

|屬性  |類型    |描述                                                       |
|----------|--------|------------------------------------------------------------------|
|羅文商店數量 |`int`  |配置給實體的儲存數量|
|密封間隔限制|`TimeSpan?`|表上的密封操作之間的間隔的可選限制。 有效範圍介於 1 到 24 小時之間。 預設值：24 小時。|
|密封閾值位元組|`int?`|對於表上的單個密封操作,需要對數據量進行可選限制。 該值的有效範圍介於 10 到 200 MB 之間。 默認值:200 MB。|

## <a name="alter-policy-streamingingestion"></a>.alter 策略流式處理

該`.alter policy streamingingestion`命令設置資料庫或表的流式處理策略。

**語法**

* `.alter``database`我的資料庫`policy``streamingingestion`*流程式處理原則物件*

* `.alter``database`我的資料庫`policy``streamingingestion``enable`

* `.alter``database`我的資料庫`policy``streamingingestion``disable`

* `.alter``table` MyTable `policy` `streamingingestion` *傳輸原則物件*

* `.alter``table`我的表`policy``streamingingestion``enable`

* `.alter``table`我的表`policy``streamingingestion``disable`

**注意事項**

1. *流式處理策略對像是*定義流引入策略物件的 JSON 物件。

2. `enable`- 如果策略未定義或使用 0 行存儲定義,則將流式引入策略設置為具有 4 個行存儲,否則該命令將不執行任何操作。

3. `disable`- 如果策略已使用正行存儲定義,則將流式引入策略設置為 0 行存儲,否則該命令將不執行任何操作。

**範例**

```kusto
.alter database MyDatabase policy streamingingestion '{  "NumberOfRowStores": 4}'

.alter table MyTable policy streamingingestion '{  "NumberOfRowStores": 4}'
```

## <a name="delete-policy-streamingingestion"></a>.刪除策略流式處理

該`.delete policy streamingingestion`命令從資料庫或表中刪除流式處理策略。

**語法** 

`.delete``database`我的資料庫`policy``streamingingestion`

`.delete``table`我的表`policy``streamingingestion`

**傳回**

該命令刪除表或資料庫流式處理策略物件,然後返回相應的[.show 策略流式處理](#show-policy-streamingingestion)命令的輸出。

**範例**

```kusto
.delete database MyDatabase policy streamingingestion 
```