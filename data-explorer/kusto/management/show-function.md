---
title: 。顯示功能-Azure 資料總管 |Microsoft Docs
description: 本文說明如何在 Azure 資料總管中顯示函式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: aa81df5ca89c1a7dc523d7799050b8a7ad3adaba
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617323"
---
# <a name="show-functions"></a>。顯示函數

列出目前選取之資料庫中的所有預存函數。
若只要傳回一個特定函式，請參閱[. show function](#show-function)。

```kusto
.show functions
```

需要[資料庫使用者](../management/access-control/role-based-authorization.md)權力。
 
|輸出參數 |類型 |描述
|---|---|--- 
|名稱  |String |函數的名稱。 
|參數  |String |函數所需的參數。
|body  |String |（零或多個）`let`語句，後面接著在函式呼叫時評估的有效 CSL 運算式。
|資料夾|String|用於 UI 函數分類的資料夾。 這個參數不會變更叫用函數的方式。
|DocString|String|UI 用途的函式描述。
 
**輸出範例** 

|名稱 |參數|body|資料夾|DocString
|---|---|---|---|---
|MyFunction1 |() | {StormEvents &#124; 限制 100}|MyFolder|簡單示範函式|
|MyFunction2 |（myLimit： long）| {StormEvents &#124; 限制 myLimit}|MyFolder|使用參數的示範函式|
|MyFunction3 |() | {StormEvents （100）}|MyFolder|函數呼叫其他函式||

## <a name="show-function"></a>.show 函式

```kusto
.show function MyFunc1
```

列出一個特定預存函數的詳細資料。 如需**所有**函式的清單，請參閱[. show 函數](#show-functions)。

**語法**

`.show``function` *FunctionName*

**輸出**

|輸出參數 |類型 |描述
|---|---|--- 
|名稱  |String |函數的名稱。 
|參數  |String |函數所需的參數。
|body  |String |（零或多個）`let`語句，後面接著在函式呼叫時評估的有效 CSL 運算式。
|資料夾|String|用於 UI 函數分類的資料夾。 此參數不會變更函數的叫用方式
|DocString|String|UI 用途的函式描述。
 
> [!NOTE] 
> * 如果函數不存在，則會傳回錯誤。
> * 需要[資料庫使用者](../management/access-control/role-based-authorization.md)權力。
 
**範例** 

```kusto
.show function MyFunction1 
```
    
|名稱 |參數 |body|資料夾|DocString
|---|---|---|---|---
|MyFunction1 |() | {StormEvents &#124; 限制 100}|MyFolder|簡單示範函式
