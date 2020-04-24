---
title: 分片策略管理 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的分片策略管理。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7770e0834e4b00f42158732e667d41eb636cec5b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520038"
---
# <a name="sharding-policy-management"></a>分片原則管理

## <a name="show-policy"></a>顯示策略

```kusto
.show table [table_name] policy sharding

.show table * policy sharding

.show database [database_name] policy sharding
```

`Show`策略顯示資料庫或表的分片策略。 如果給定名稱為"*",則顯示給定實體類型(資料庫或表)的所有策略。

### <a name="output"></a>輸出

|原則名稱 | 實體名稱 | 原則 | 子實體 | 實體類型
|---|---|---|---|---
|為縮小字型功能放大縮小字型功能 | 資料庫/表名稱 | 表示策略的 json 格式字串 | 表列表(用於資料庫)|資料庫/表

## <a name="alter-policy"></a>變更原則

### <a name="examples"></a>範例

以下範例返回實體的更新擴展區分片策略,其中資料庫或表指定為限定名稱,作為其輸出。

#### <a name="setting-all-properties-of-the-policy-explicitly-at-table-level"></a>在表層級設定策略的所有屬性

```kusto
.alter table [table_name] policy sharding 
@'{ "MaxRowCount": 750000, "MaxExtentSizeInMb": 1024, "MaxOriginalSizeInMb": 2048}'
```

#### <a name="setting-all-properties-of-the-policy-explicitly-at-database-level"></a>在資料庫等級顯示式設定策略的所有屬性

```kusto
.alter database [database_name] policy sharding
@'{ "MaxRowCount": 750000, "MaxExtentSizeInMb": 1024, "MaxOriginalSizeInMb": 2048}'
```

#### <a name="setting-the-default-sharding-policy-at-database-level"></a>在資料庫等級設定*預設*分片原則

```kusto
.alter database [database_name] policy sharding @'{}'
```

#### <a name="altering-a-single-property-of-the-policy-at-database-level"></a>在資料庫等級變更策略的單個屬性 

保持所有其他屬性保持正。

```kusto
.alter-merge database [database_name] policy sharding
@'{ "MaxExtentSizeInMb": 1024}'
```

#### <a name="altering-a-single-property-of-the-policy-at-table-level"></a>在表層變更策略的單一屬性

保持所有其他屬性保持正點

```kusto
.alter-merge table [table_name] policy sharding
@'{ "MaxRowCount": 750000}'
```

## <a name="delete-policy"></a>移除原則

```kusto
.delete table [table_name] policy sharding

.delete database [database_name] policy sharding
```

該命令刪除給定實體的當前分片策略。