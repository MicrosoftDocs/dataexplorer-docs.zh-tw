---
title: .alter 函數文件字串 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 .alter 函數文檔字串。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 25f6dac67f65add545f7215b44daafb0257a8375
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522571"
---
# <a name="alter-function-docstring"></a>.alter 函式文件字串

更改現有函數的 DocString 值。

`.alter``function`*功能名稱*`docstring`*文件*

> [!NOTE]
> * 需要[資料庫管理員權限](../management/access-control/role-based-authorization.md)
> * 允許最初創建函數的[資料庫使用者](../management/access-control/role-based-authorization.md)修改該函數。 
> * 如果函數不存在,則返回錯誤。 有關建立新函數,請參閱[.create 函數](create-function.md)

|輸出參數 |類型 |描述
|---|---|--- 
|名稱  |String |函數的名稱。 
|參數  |String |函數所需的參數。
|body  |String |(零或更多)`let`語句後跟在函數調用時計算的有效 CSL 運算式。
|資料夾|String|用於 UI 函數分類的資料夾。 此參數不會更改呼叫函數的方式。
|文件字串|String|用於 UI 目的的函數說明。

**範例** 

```
.alter function MyFunction1 docstring "Updated docstring"
```
    
|名稱 |參數 |body|資料夾|文件字串
|---|---|---|---|---
|My功能2 |(我的限制:長)| [風暴事件&#124;限制我的限制]|我的資料夾|更新的文件字串|