---
title: current_cluster_endpoint() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的current_cluster_endpoint()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 65ce130b4dd3e0a3125eefc6c410775647f9b964
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516842"
---
# <a name="current_cluster_endpoint"></a>current_cluster_endpoint()

返回要查詢的當前群集的網路終結點(DNS 名稱)。

**語法**

`current_cluster_endpoint()`

**傳回**

正在查詢的當前群集的網路終結點(DNS 名稱)作為`string`類型的值。

**範例**

```kusto
print strcat("This query executed on: ", current_cluster_endpoint())
```