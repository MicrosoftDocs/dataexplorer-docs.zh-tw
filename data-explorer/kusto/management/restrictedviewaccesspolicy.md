---
title: 受限檢視存取策略 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的受限檢視訪問策略。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 6f994f5b80632650ab6dbe5dcf28cd82407d688f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520412"
---
# <a name="restrictedviewaccess-policy"></a>受限檢視存取原則

*受限檢視訪問*是一種可選策略,可在資料庫中的表上啟用。

在表上啟用此策略時,*只能*查詢表中的數據到添加到資料庫中的[「無限制檢視器」](../management/access-control/role-based-authorization.md)角色的主體。

在表上啟用策略時,任何未包含在[「無限制檢視器](../management/access-control/role-based-authorization.md)」資料庫等級角色中的主體(甚至表/資料庫/群集管理員)將無法查詢表中的數據。

["無限制查看器"](../management/access-control/role-based-authorization.md)角色授予資料庫中所有啟用策略*的表的*檢視許可權,前提是當前主體已授權查詢資料庫(資料庫管理員/使用者/查看器)。 向角色添加或刪除主體可以由[資料庫管理員](../management/access-control/role-based-authorization.md)完成。

> [!NOTE]
> 無法在啟用[行級別安全原則](./rowlevelsecuritypolicy.md)的表上配置受限檢視存取策略。

有關管理受限檢視存取策略的控制命令,[請參考此處](../management/restrictedviewaccess-policy.md)。