---
title: . 建立函式-Azure 資料總管 |Microsoft Docs
description: 本文說明如何在 Azure 資料總管中建立函數。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f0a509e33ca1bc4c6d4a400428898b7c241222a2
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320973"
---
# <a name="create-function"></a>.create 函式

建立預存函式，這是具有指定名稱的可重複使用[ `let` 語句](../query/letstatement.md)函數。 函式定義會隨著資料庫中繼資料保存。

函式可以呼叫其他函式 (不支援 recursiveness) ，而且語句可作為函式 `let` *主體* 的一部分。 請參閱[ `let` 語句](../query/letstatement.md)。

參數類型和 CSL 語句的規則與 for [ `let` 語句](../query/letstatement.md)的規則相同。
    
**語法**

`.create``function`[ `ifnotexists` ] [ `with` `(` [ `docstring` `=` *檔*] [ `,` `folder` `=` *資料夾*] `)` ] [ `,` `skipvalidation` `=` ' true '] `)` ] *FunctionName* `(` *ParamName* `:` *ParamType* [ `,` ...] `)` `{` *FunctionBody*`}`

**輸出**    
    
|輸出參數 |類型 |描述
|---|---|--- 
|Name  |String |函式的名稱。 
|參數  |String |函數所需的參數。
|主體  |String | (零或多個) `let` 語句，後面接著在函式呼叫時評估的有效 CSL 運算式。
|資料夾|String|用於 UI 函數分類的資料夾。 此參數不會變更叫用函數的方式。
|DocString|String|適用于 UI 用途的函式描述。

> [!NOTE]
> * 如果函數已經存在：
>    * 如果 `ifnotexists` 指定了旗標，則會忽略此命令， (未套用任何變更) 。
>    * 如果 `ifnotexists` 未指定旗標，則會傳回錯誤。
>    * 若要改變現有的函式，請參閱 [`.alter function`](alter-function.md)
> * 需要 [資料庫使用者](../management/access-control/role-based-authorization.md)權力。
> * 語句中不支援所有資料類型 `let` 。 支援的類型為： boolean、string、long、datetime、timespan、double 和 dynamic。
> * 使用 ' skipvalidation ' 略過函式的語意驗證。 當函式以不正確的順序建立，而使用 F2 的 F1 是稍早建立時，這非常有用。

**範例** 

```kusto
.create function 
with (docstring = 'Simple demo function', folder='Demo')
MyFunction1()  {StormEvents | limit 100}
```

|名稱|參數|主體|資料夾|DocString|
|---|---|---|---|---|
|MyFunction1|()|{StormEvents &#124; 限制 100}|示範|簡單的示範函數|

```kusto
.create function
with (docstring = 'Demo function with parameter', folder='Demo')
 MyFunction2(myLimit: long)  {StormEvents | limit myLimit}
```

|名稱|參數|主體|資料夾|DocString|
|---|---|---|---|---|
|MyFunction2| (myLimit： long) |{StormEvents &#124; limit myLimit}|示範|使用參數的 Demo 函式|
