---
title: current_database() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 current_database()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c61717bbc8d202624b36088df5aed2ba3f3a8d2d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516740"
---
# <a name="current_database"></a>current_database()

返回作用域中的資料庫的名稱(如果未指定其他資料庫,則解析所有查詢實體的資料庫)。

**語法**

`current_database()`

**傳回**

作用網域中的資料庫的名稱作為`string`類型的值。

**範例**

```kusto
print strcat("Database in scope: ", current_database())
```