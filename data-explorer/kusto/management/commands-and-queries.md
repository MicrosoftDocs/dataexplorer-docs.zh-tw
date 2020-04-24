---
title: 指令與查詢管理 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的命令和查詢管理。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/19/2019
ms.openlocfilehash: f8c01709d69a0b439ce51b4782eb8f747db15d65
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521908"
---
# <a name="commands-and-queries-management"></a>命令與查詢管理

## <a name="show-commands-and-queries"></a>.顯示指令與查詢 

`.show``commands-and-queries`返回具有已達到最終狀態的管理員命令和查詢的表。 這些命令和查詢可供查詢 30 天。

命令輸出中提供的資訊與[.show 命令](commands.md)和[.show 查詢](queries.md)提供的資訊類似,但它基本上允許以簡單的方式將兩個結果集合並。

**語法**

`.show` `commands-and-queries`
 
**輸出**
 
輸出架構如下:

| ColumnName               | ColumnType |
|--------------------------|------------|
| 用戶端活動Id         | 字串     |
| CommandType              | 字串     |
| Text                     | 字串     |
| 資料庫                 | 字串     |
| 啟動                | Datetime   |
| 上次更新時間            | Datetime   |
| Duration                 | 時間範圍   |
| State                    | 字串     |
| FailureReason            | 字串     |
| RootActivityId           | guid       |
| User                     | 字串     |
| Application              | 字串     |
| 主體                | 字串     |
| 用戶端要求屬性  | 動態    |
| 總Cpu                 | 時間範圍   |
| 記憶體峰值               | long       |
| 快取統計資訊          | 動態    |
| 掃描範圍統計 | 動態    |
| 結果集統計      | 動態    |

請注意,對於查詢,值`CommandType`為`Query`。