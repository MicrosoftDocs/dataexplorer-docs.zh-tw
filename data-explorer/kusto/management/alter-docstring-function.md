---
title: 。 alter function docstring-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 alter function docstring。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: d590e93a6772483aba6b9580b26490eb2fe5ec08
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617845"
---
# <a name="alter-function-docstring"></a>。 alter function docstring

改變現有函數的 DocString 值。

`.alter``function` *FunctionName* FunctionName `docstring` *檔*

> [!NOTE]
> * 需要[資料庫管理員許可權](../management/access-control/role-based-authorization.md)
> * 原先建立函數的[資料庫使用者](../management/access-control/role-based-authorization.md)可以修改函式。 
> * 如果函數不存在，則會傳回錯誤。 如需建立新的函式，請參閱[. create function](create-function.md)

|輸出參數 |類型 |描述
|---|---|--- 
|名稱  |String |函數的名稱。 
|參數  |String |函數所需的參數。
|body  |String |（零或多個）`let`語句，後面接著在函式呼叫時評估的有效 CSL 運算式。
|資料夾|String|用於 UI 函數分類的資料夾。 這個參數不會變更叫用函數的方式。
|DocString|String|UI 用途的函式描述。

**範例** 

```kusto
.alter function MyFunction1 docstring "Updated docstring"
```
    
|名稱 |參數 |body|資料夾|DocString
|---|---|---|---|---
|MyFunction2 |（myLimit： long）| {StormEvents &#124; 限制 myLimit}|MyFolder|已更新 docstring|