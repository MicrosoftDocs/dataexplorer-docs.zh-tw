---
title: .alter 函數 - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 .alter 函數。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 3fb7b3aab9d140d5f3660ef1384bf54efa9cd825
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522452"
---
# <a name="alter-function"></a>.alter 函數

更改現有函數並將其存儲在資料庫元數據中。
參數類型和 CSL 語句的規則與[`let`語句](../query/letstatement.md)的規則相同。

**語法**

```
.alter function [with (docstring = '<description>', folder='<name>', skipvalidation='true')] [FunctionName] ([paramName:paramType], ...) { CSL-statement }
```
    
|輸出參數 |類型 |描述
|---|---|--- 
|名稱  |String |函數的名稱。
|參數  |String |函數所需的參數。
|body  |String |(零或更多)`let`語句後跟在函數調用時計算的有效 CSL 運算式。
|資料夾|String|用於 UI 函數分類的資料夾。 此參數不會更改呼叫函數的方式。
|文件字串|String|用於 UI 目的的函數說明。

> [!NOTE]
> * 如果函數不存在,則返回錯誤。 有關建立新函數,請參閱[.create 函數](create-function.md)
> * 需要[資料庫管理員權限](../management/access-control/role-based-authorization.md)
> * 允許最初創建函數的[資料庫使用者](../management/access-control/role-based-authorization.md)修改該函數。 
> * 語句中`let`並非所有 Kusto 類型都支援。 支援的類型包括:字串、長、日期時間、時間跨度和雙精度值。
> * 用於`skipvalidation`跳過函數的語義驗證。 當以錯誤的順序創建函數,並且使用 F2 的 F1 在前面創建時,這非常有用。
 
**範例** 

```
.alter function
with (docstring = 'Demo function with parameter', folder='MyFolder')
 MyFunction2(myLimit: long)  {StormEvents | limit myLimit}
``` 
    
|名稱 |參數 |body|資料夾|文件字串
|---|---|---|---|---
|My功能2 |(我的限制:長)| [風暴事件&#124;限制我的限制]|我的資料夾|帶參數的簡報|