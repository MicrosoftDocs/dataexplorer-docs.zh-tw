---
title: current_principal_details() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的current_principal_details()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/08/2019
ms.openlocfilehash: 5418647c811b034bb5790dfff3fd17f500c52db0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516774"
---
# <a name="current_principal_details"></a>current_principal_details()

返回運行查詢的主體的詳細資訊。

**語法**

`current_principal_details()`

**傳回**

目前主體作為的詳細資訊`dynamic`。

**範例**

```kusto
print d=current_principal_details()
```

|d|
|---|
|{<br>  "使用者主名":""user@fabrikam.com<br>  "身份提供者":""https://sts.windows.net<br>  "授權":"72f988bf-86f1-41af-91ab-2d7cd011db47"<br>  "Mfa":"真實"<br>  "類型":"aadUser"<br>  "顯示名稱":"詹姆斯·史密斯(上名:)" user@fabrikam.com<br>  "ObjectId":"346e950e-4a62-42bf-96f5-4cf4eac3f11e",<br>  」FQN":null,<br>  備註":null<br>}|
