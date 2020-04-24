---
title: 取得月() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 getmonth()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/22/2020
ms.openlocfilehash: b0224d8ea9c99ce72604a5b7df248394bc6317fa
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514411"
---
# <a name="getmonth"></a>getmonth()

從日期時間取得月份 (1-12)。

另一個別名:月年()

**範例**

```kusto
print month = getmonth(datetime(2015-10-12))
```