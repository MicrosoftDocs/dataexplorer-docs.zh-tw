---
title: . alter function 資料夾-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 alter function 資料夾。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 37cedb7ba6e58f5b434101cd23b876cf18c2b78c
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321721"
---
# <a name="alter-function-folder"></a>.alter function folder

改變現有函數的資料夾值。

`.alter``function` *FunctionName* `folder` *資料夾*

> [!NOTE]
> * 需要 [資料庫管理員許可權](../management/access-control/role-based-authorization.md)
> * 原先建立函數的 [資料庫使用者](../management/access-control/role-based-authorization.md) 可以修改函數。 
> * 如果函數不存在，就會傳回錯誤。 若要建立新的函式， [`.create function`](create-function.md)

|輸出參數 |類型 |描述
|---|---|--- 
|Name  |String |函式的名稱。 
|參數  |String |函數所需的參數。
|主體  |String | (零或多個) Let 語句後面接著有效的 CSL 運算式，並在函式呼叫時進行評估。
|資料夾|String|用於 UI 函數分類的資料夾。 此參數不會變更叫用函數的方式。
|DocString|String|適用于 UI 用途的函式描述。

**範例** 

```kusto
.alter function MyFunction1 folder "Updated Folder"
```
    
|名稱 |參數 |主體|資料夾|DocString
|---|---|---|---|---
|MyFunction2 | (myLimit： long) | {StormEvents &#124; limit myLimit}|更新的資料夾|部分 DocString|

```kusto
.alter function MyFunction1 folder @"First Level\Second Level"
```
    
|名稱 |參數 |主體|資料夾|DocString
|---|---|---|---|---
|MyFunction2 | (myLimit： long) | {StormEvents &#124; limit myLimit}|第一個 Level\Second 層級|部分 DocString|