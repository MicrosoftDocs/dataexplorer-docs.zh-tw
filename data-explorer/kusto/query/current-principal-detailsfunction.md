---
title: current_principal_details （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 current_principal_details （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/08/2019
ms.openlocfilehash: f71770d2cc9d44987731a247fa8eb945ed323391
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227500"
---
# <a name="current_principal_details"></a>current_principal_details()

傳回執行查詢之主體的詳細資料。

**語法**

`current_principal_details()`

**傳回**

目前主體的詳細資料，以做為 `dynamic` 。

**範例**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print d=current_principal_details()
```

|d|
|---|
|{<br>  "UserPrincipalName"： " user@fabrikam.com "，<br>  "IdentityProvider"： " https://sts.windows.net "，<br>  「授權」： "72f988bf-86f1-41af-91ab-2d7cd011db47"，<br>  "Mfa"： "True"，<br>  "Type"： "以 aaduser name>"，<br>  "DisplayName"： "James Smith （upn： user@fabrikam.com ）"，<br>  "ObjectId"： "346e950e-4a62-42bf-96f5-4cf4eac3f11e"，<br>  "FQN"： null、<br>  「附注」： null<br>}|
