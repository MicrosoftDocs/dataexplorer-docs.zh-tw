---
title: .drop 函數 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 .drop 函數。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8930f7333ff18fad0d5b3dbbebe9328f8bf7a0b9
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744785"
---
# <a name="drop-function"></a>.drop 函式

從資料庫中刪除函數。
有關從資料庫中刪除多個函數,請參閱[.drop 函數](#drop-functions)。

**語法**

`.drop``function`*函數名稱*`ifexists`|

* `ifexists`:如果指定,則修改命令的行為,使不存在的函數不會失敗。

> [!NOTE]
> * 需要[資料庫管理權限](../management/access-control/role-based-authorization.md)。
> * 允許最初創建函數的[資料庫使用者](../management/access-control/role-based-authorization.md)修改該函數。  
    
|輸出參數 |類型 |描述
|---|---|--- 
|名稱  |String |已刪除的函式名稱
 
**範例** 

```kusto
.drop function MyFunction1
```

## <a name="drop-functions"></a>.drop 函數

從資料庫中刪除多個函數。

**語法**

`.drop``functions` (*功能名稱 1*,*功能名稱 2*,.. )[如果存在]

**傳回**

此命令返回資料庫中其餘函數的清單。

|輸出參數 |類型 |描述
|---|---|--- 
|名稱  |String |函數的名稱。 
|參數  |String |函數所需的參數。
|body  |String |(零或更多)`let`語句後跟在函數調用時計算的有效 CSL 運算式。
|資料夾|String|用於 UI 函數分類的資料夾。 此參數不會更改呼叫函數的方式。
|文件字串|String|用於 UI 目的的函數說明。

需要[函式管理權限](../management/access-control/role-based-authorization.md)。

**範例** 
 
```kusto
.drop functions (Function1, Function2, Function3) ifexists
```
