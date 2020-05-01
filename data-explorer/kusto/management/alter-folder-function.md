---
title: 。 alter function 資料夾-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 alter function 資料夾。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 6becb5e29fd5771e1027c824b5a3539ed3c33b88
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617828"
---
# <a name="alter-function-folder"></a>。 alter function 資料夾

改變現有函數的資料夾值。

`.alter``function` *FunctionName* FunctionName `folder` *資料夾*

> [!NOTE]
> * 需要[資料庫管理員許可權](../management/access-control/role-based-authorization.md)
> * 原先建立函數的[資料庫使用者](../management/access-control/role-based-authorization.md)可以修改函式。 
> * 如果函數不存在，則會傳回錯誤。 建立新函式， [. create function](create-function.md)

|輸出參數 |類型 |描述
|---|---|--- 
|名稱  |String |函數的名稱。 
|參數  |String |函數所需的參數。
|body  |String |（零或多個）Let 語句，後面接著在函式呼叫時評估的有效 CSL 運算式。
|資料夾|String|用於 UI 函數分類的資料夾。 這個參數不會變更函數的叫用方式。
|DocString|String|UI 用途的函式描述。

**範例** 

```kusto
.alter function MyFunction1 folder "Updated Folder"
```
    
|名稱 |參數 |body|資料夾|DocString
|---|---|---|---|---
|MyFunction2 |（myLimit： long）| {StormEvents &#124; 限制 myLimit}|已更新資料夾|一些 DocString|

```kusto
.alter function MyFunction1 folder @"First Level\Second Level"
```
    
|名稱 |參數 |body|資料夾|DocString
|---|---|---|---|---
|MyFunction2 |（myLimit： long）| {StormEvents &#124; 限制 myLimit}|第一個 Level\Second 層級|一些 DocString|