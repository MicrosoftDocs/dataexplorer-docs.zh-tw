---
title: 資料庫 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的資料庫。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: a94b168d8aa8443251f3a01dc659e114b0aacaf5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509430"
---
# <a name="databases"></a>資料庫

Kusto 遵循一個關係模型,用於存儲上層實體`database`為 的數據。 

Kusto 群集可以承載多個資料庫,其中每個資料庫將承載自己的[表](tables.md)、[存儲函數](stored-functions.md)和[外部表](externaltables.md)的集合。
每個資料庫都有自己的權限集,基於[角色存取控制 (RBAC) 模型](../../management/access-control/index.md)

**注意事項**  

* 資料庫名稱不區分大小寫。
* 資料庫名稱必須遵循[實體名稱](./entity-names.md)的規則。
* 每個群集的資料庫的最大限制為 10,000。