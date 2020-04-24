---
title: 每小時() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的小時()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 08fdb45af5eec7f71d491725ea58a7d72c06371b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514071"
---
# <a name="hourofday"></a>hourofday()

傳回表示給定日期的小時數的整數

```kusto
hourofday(datetime(2015-12-14 18:54)) == 18
```

**語法**

`hourofday(`*a_date*`)`

**引數**

* `a_date`：`datetime`。

**傳回**

`hour number`當天 (0-23)。