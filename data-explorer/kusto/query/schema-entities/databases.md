---
title: 資料庫-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的資料庫。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 3d981a4b56eef689081a4bc6a622221c126efa2e
ms.sourcegitcommit: 8b8228fe18e6408673891374a8048a7a3723921c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96516059"
---
# <a name="databases"></a>資料庫

Kusto 會遵循將資料儲存在最上層實體為的關聯性模型 `database` 。 

Kusto 叢集可以裝載數個資料庫，其中每個資料庫都會裝載自己的 [資料表](tables.md)、 [預存函數](stored-functions.md)和 [外部資料表](externaltables.md)集合。
每個資料庫都有自己的許可權集合，以角色型存取控制為基礎， [ (RBAC) 模型](../../management/access-control/index.md)

**注意事項**  

* 資料庫名稱必須遵循 [機構名稱](./entity-names.md)的規則。
* 每個群集的資料庫上限為10000。
