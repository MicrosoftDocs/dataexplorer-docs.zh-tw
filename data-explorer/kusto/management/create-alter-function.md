---
title: .創建或更改函數 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 .創建或更改函數。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 9e9c24f7fda44d6c44b8f78d8622b525268a341a
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744286"
---
# <a name="create-or-alter-function"></a>.create-or-alter 函式

創建存儲的函數或更改現有函數並將其存儲在資料庫元資料中。

```kusto
.create-or-alter function [with (docstring = '<description>', folder='<name>')] [FunctionName] ([paramName:paramType], ...) { CSL-statement }
```

如果提供的*函數Name*函數在資料庫中不存在,則該命令將創建一個新函數。 如果函數已存在,則該函數將更改。

**範例**

```kusto
.create-or-alter function  with (docstring = 'Demo function with parameter', folder='MyFolder') TestFunction(myLimit:int)
{
    StormEvents | take myLimit 
} 
```

|名稱|參數|body|資料夾|文件字串|
|---|---|---|---|---|
|測試功能|(myLimit:int)|[ 風暴事件&#124;採取我的極限 *|我的資料夾|帶參數的簡報|
