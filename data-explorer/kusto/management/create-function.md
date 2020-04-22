---
title: .創建函數 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 .create 函數。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bc2caa5dcc886964b42bf1915bf3a003f2d32c7a
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744452"
---
# <a name="create-function"></a>.create 函式

創建存儲函數,該函數是具有給定名稱的可重用[`let`語句](../query/letstatement.md)函數。 函數定義隨資料庫元數據一起保留。

函數可以調用其他函數(不支援遞歸性),並允許`let`語句作為*函數體的*一部分。 請參考[`let`敘述](../query/letstatement.md)。

參數類型和 CSL 語句的規則與[`let`語句](../query/letstatement.md)的規則相同。
    
**語法**

`.create``function` [`ifnotexists``with` `(` ]`,` `folder` `)` `)``,` `:` *Documentation*`,``skipvalidation``(` *ParamName* *FolderName* *ParamType* *FunctionName* [ ]`=`文件 [`=`資料夾名稱 】 [ " true"] [ 函數名稱 Paramname ParamType ] [ ] `docstring` `=``)`*文*`{``}`

**輸出**    
    
|輸出參數 |類型 |描述
|---|---|--- 
|名稱  |String |函數的名稱。 
|參數  |String |函數所需的參數。
|body  |String |(零或更多)`let`語句後跟在函數調用時計算的有效 CSL 運算式。
|資料夾|String|用於 UI 函數分類的資料夾。 此參數不會更改呼叫函數的方式。
|文件字串|String|用於 UI 目的的函數說明。

> [!NOTE]
> * 如果函數已存在:
>    * 如果`ifnotexists`指定了標誌,則該命令將被忽略(不應用任何更改)。
>    * 如果未`ifnotexists`指定標誌,則傳回錯誤。
>    * 有關更改現有函數,請參閱[.alter 函數](alter-function.md)
> * 需要[資料庫使用者權限](../management/access-control/role-based-authorization.md)。
> * 語句中`let`並非所有數據類型都受支援。 支援的類型包括:布爾、字串、長、日期時間、時間跨度、雙精度值和動態。
> * 使用「跳過驗證」跳過函數的語義驗證。 當以錯誤的順序創建函數,並且使用 F2 的 F1 在前面創建時,這非常有用。

**範例** 

```kusto
.create function 
with (docstring = 'Simple demo function', folder='Demo')
MyFunction1()  {StormEvents | limit 100}
```

|名稱|參數|body|資料夾|文件字串|
|---|---|---|---|---|
|我的功能1|()|[風暴事件&#124;限制 100}|示範|簡易簡報功能|

```kusto
.create function
with (docstring = 'Demo function with parameter', folder='Demo')
 MyFunction2(myLimit: long)  {StormEvents | limit myLimit}
```

|名稱|參數|body|資料夾|文件字串|
|---|---|---|---|---|
|My功能2|(myLimit:長)|[風暴事件&#124;限制我的限制]|示範|帶參數的簡報|
