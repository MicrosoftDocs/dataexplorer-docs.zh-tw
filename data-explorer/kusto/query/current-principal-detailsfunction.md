---
title: 'current_principal_details ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 current_principal_details。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/08/2019
ms.openlocfilehash: 387504d49b2c8be52357be74e69cdbde581c1298
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252533"
---
# <a name="current_principal_details"></a>current_principal_details()

傳回執行查詢之主體的詳細資料。

## <a name="syntax"></a>語法

`current_principal_details()`

## <a name="returns"></a>傳回

的目前主體詳細資料 `dynamic` 。

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print d=current_principal_details()
```

|d|
|---|
|{<br>  "UserPrincipalName"： " user@fabrikam.com "，<br>  "IdentityProvider"： " https://sts.windows.net "，<br>  "授權單位"： "72f988bf-86f1-41af-91ab-2d7cd011db47"，<br>  "Mfa"： "True"，<br>  "Type"： "AadUser"，<br>  "DisplayName"： "James Smith (upn： user@fabrikam.com) "，<br>  "ObjectId"： "346e950e-4a62-42bf-96f5-4cf4eac3f11e"，<br>  "FQN"： null、<br>  「附注」： null<br>}|
