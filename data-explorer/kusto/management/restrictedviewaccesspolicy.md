---
title: Kusto RestrictedViewAccess 原則控制查詢-Azure 資料總管
description: 本文說明 Azure 資料總管中的 RestrictedViewAccess 原則。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: be9eea23373489faf44c70146109fe1488877269
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967548"
---
# <a name="restricted-view-access-policy"></a>受限的檢視存取原則

*RestrictedViewAccess*是選擇性的原則，可以針對資料庫的資料表啟用。

針對資料表啟用此原則時，只有在資料庫中具有[UnrestrictedViewer](../management/access-control/role-based-authorization.md)角色的主體，才可以查詢資料表中的資料。
未向[UnrestrictedViewer](../management/access-control/role-based-authorization.md)資料庫層級角色註冊的任何主體，將無法查詢資料表中的資料。 即使是未註冊的資料表/資料庫/叢集管理員也是如此。

[UnrestrictedViewer](../management/access-control/role-based-authorization.md)角色會授與資料庫中已啟用原則之*所有*資料表的 view 許可權。
目前的主體（資料庫管理員/使用者/檢視器）已獲授權可查詢資料庫。 新增或移除主體可以透過[DatabaseAdmin](../management/access-control/role-based-authorization.md)來完成。

> [!NOTE]
> 無法在啟用[資料列層級安全性原則](./rowlevelsecuritypolicy.md)的資料表上設定 RestrictedViewAccess 原則。

如需詳細資訊，請參閱管理[RestrictedViewAccess 原則](../management/restrictedviewaccess-policy.md)的控制命令。
