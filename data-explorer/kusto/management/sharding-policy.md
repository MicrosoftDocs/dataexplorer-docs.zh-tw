---
title: 分區化原則管理-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的分區化原則管理。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fb4ff4ade5e3fb0e2f01de0adc74aecd27381a3d
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967463"
---
# <a name="sharding-policy-command"></a>分區化原則命令

## <a name="show-policy"></a>顯示原則

```kusto
.show table [table_name] policy sharding

.show table * policy sharding

.show database [database_name] policy sharding
```

`Show`[原則] 會顯示資料庫或資料表的分區化原則。 如果指定的名稱是 ' * '，它會顯示指定之實體類型（資料庫或資料表）的所有原則。

### <a name="output"></a>輸出

|原則名稱 | 實體名稱 | 原則 | 子實體 | 實體類型
|---|---|---|---|---
|ExtentsShardingPolicy | 資料庫/資料表名稱 | 代表原則的 json 格式字串 | 資料表清單（適用于資料庫）|資料庫/資料表

## <a name="alter-policy"></a>改變原則

### <a name="examples"></a>範例

下列範例會傳回實體的更新定界分割化原則，並將資料庫或資料表指定為限定名稱，做為其輸出。

#### <a name="setting-all-properties-of-the-policy-explicitly-at-table-level"></a>在資料表層級明確設定原則的所有屬性

```kusto
.alter table [table_name] policy sharding 
@'{ "MaxRowCount": 750000, "MaxExtentSizeInMb": 1024, "MaxOriginalSizeInMb": 2048}'
```

#### <a name="setting-all-properties-of-the-policy-explicitly-at-database-level"></a>在資料庫層級明確設定原則的所有屬性

```kusto
.alter database [database_name] policy sharding
@'{ "MaxRowCount": 750000, "MaxExtentSizeInMb": 1024, "MaxOriginalSizeInMb": 2048}'
```

#### <a name="setting-the-default-sharding-policy-at-database-level"></a>在資料庫層級設定*預設*分區化原則

```kusto
.alter database [database_name] policy sharding @'{}'
```

#### <a name="altering-a-single-property-of-the-policy-at-database-level"></a>在資料庫層級改變原則的單一屬性 

保留所有其他屬性。

```kusto
.alter-merge database [database_name] policy sharding
@'{ "MaxExtentSizeInMb": 1024}'
```

#### <a name="altering-a-single-property-of-the-policy-at-table-level"></a>在資料表層級改變原則的單一屬性

保留所有其他屬性

```kusto
.alter-merge table [table_name] policy sharding
@'{ "MaxRowCount": 750000}'
```

## <a name="delete-policy"></a>刪除原則

```kusto
.delete table [table_name] policy sharding

.delete database [database_name] policy sharding
```

命令會刪除指定實體的目前分區化原則。