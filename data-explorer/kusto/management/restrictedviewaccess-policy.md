---
title: 受限檢視存取策略 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的受限檢視訪問策略。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 9fcf37d30bfe3ab0f9c4b5d4a720e6a5ba4ffe34
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520446"
---
# <a name="restrictedviewaccess-policy"></a>受限檢視存取原則

*此處記錄了受限視圖訪問*策略[here](../management/restrictedviewaccesspolicy.md)。

在資料庫中的表上啟用或關閉策略的控制命令如下:

要啟用/停用策略,請進行以下工作:
```kusto
.alter table TableName policy restricted_view_access true|false
```

要啟用/停用多個表的策略,請進行以下操作:
```kusto
.alter tables (TableName1, ..., TableNameN) policy restricted_view_access true|false
```

要檢視原則:
```kusto
.show table TableName policy restricted_view_access  

.show table * policy restricted_view_access  
```

要刪除政策 (等效於關閉):
```kusto
.delete table TableName policy restricted_view_access  
```