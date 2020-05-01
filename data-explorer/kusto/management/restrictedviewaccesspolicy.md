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
ms.openlocfilehash: e44aa2b14aa8babdab95a6ad8c6f7ef5ed026ff9
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617421"
---
# <a name="restrictedviewaccess-policy"></a>RestrictedViewAccess 原則

*RestrictedViewAccess*是選擇性的原則，可以在資料庫的資料表上啟用。

在資料表上啟用此原則時，資料表中的資料*只*會查詢到資料庫中新增至[UnrestrictedViewer](../management/access-control/role-based-authorization.md)角色的主體。

在資料表上啟用原則時，任何未包含在[UnrestrictedViewer](../management/access-control/role-based-authorization.md)資料庫層級角色中的主體（甚至是資料表/資料庫/叢集系統管理員），將無法查詢資料表中的資料。

[UnrestrictedViewer](../management/access-control/role-based-authorization.md)角色會授與資料庫中已啟用原則之*所有*資料表的 view 許可權，前提是目前的主體已獲授權可以查詢資料庫（資料庫管理員/使用者/檢視器）。 新增或移除角色的主體可以透過[DatabaseAdmin](../management/access-control/role-based-authorization.md)來完成。

> [!NOTE]
> 無法在啟用[資料列層級安全性原則](./rowlevelsecuritypolicy.md)的資料表上設定 RestrictedViewAccess 原則。

如需管理 RestrictedViewAccess 原則之控制命令的詳細資訊，[請參閱這裡](../management/restrictedviewaccess-policy.md)。