---
title: 。 alter function docstring-Azure 資料總管
description: 本文說明 Azure 資料總管中的 alter function docstring。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: e364a3cc5607b1a4b629954c93bf90e9173c00f1
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321755"
---
# <a name="alter-function-docstring"></a>.alter function docstring

改變 `DocString` 現有函數的值。

`.alter``function` *FunctionName* `docstring` *檔*

> [!NOTE]
> * 需要 [資料庫管理員許可權](../management/access-control/role-based-authorization.md)
> * 原先建立函數的 [資料庫使用者](../management/access-control/role-based-authorization.md) 可以修改函數。
> * 如果函數不存在，就會傳回錯誤。 如需如何建立新函數的詳細資訊，請參閱 [`.create function`](create-function.md) 。

|輸出參數 |類型 |描述
|---|---|--- 
|Name  |String |函式的名稱
|參數  |String |函數所需的參數
|主體  |String | (零或多個) 語句，後面接著叫用函式 `let` 時所評估的有效 CSL 運算式
|資料夾|String|用於 UI 函數分類的資料夾。 此參數不會變更叫用函數的方式
|DocString|String|UI 用途的函式描述

**範例** 

```kusto
.alter function MyFunction1 docstring "Updated docstring"
```
    
|名稱 |參數 |主體|資料夾|DocString
|---|---|---|---|---
|MyFunction2 | (myLimit： long) | {StormEvents &#124; limit myLimit}|MyFolder|已更新 docstring|
