---
title: 資料庫管理-Azure 資料總管
description: 本文說明 Azure 資料總管中的資料庫管理。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 9dc2e1bf489123ddd66c9a203d74284e36eda53e
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320939"
---
# <a name="databases-management"></a>管理資料庫

本主題說明下列資料庫控制命令：

|命令 |描述 |
|--------|------------|
|[`.show databases`](show-databases.md) |傳回資料表，其中每個記錄對應至使用者可存取的叢集中的資料庫|
|[`.show database`](show-database.md) |傳回資料表，其中顯示內容資料庫的屬性 |
|[`.show cluster databases`](show-cluster-database.md) |傳回資料表，其中顯示附加至叢集的所有資料庫，以及叫用命令的使用者具有存取權的所有資料庫 |
|[`.alter database`](alter-database.md) |改變資料庫相當 (易記) 名稱 |
|[`.show database schema`](show-schema-database.md) |傳回所選資料庫結構的一般清單，以及其在單一資料表或 JSON 物件中的所有資料表和資料行 |