---
title: 。 create-或-alter function-Azure 資料總管
description: 本文說明 Azure 資料總管中的 create-或-alter 函數。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: f19ca38f344f10b9dd8e4491b467eaad5ca022bc
ms.sourcegitcommit: a034b6a795ed5e62865fcf9340906f91945b3971
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2020
ms.locfileid: "85197237"
---
# <a name="create-or-alter-function"></a>.create-or-alter 函式

建立預存函數或改變現有的函式，並將它儲存在資料庫中繼資料內。

```kusto
.create-or-alter function [with (docstring = '<description>', folder='<name>')] [FunctionName] ([paramName:paramType], ...) { CSL-statement }
```

如果具有所提供*FunctionName*的函式不存在於資料庫中繼資料中，此命令會建立新的函式。 否則，該函數將會變更。

**範例**

```kusto
.create-or-alter function  with (docstring = 'Demo function with parameter', folder='MyFolder') TestFunction(myLimit:int)
{
    StormEvents | take myLimit 
} 
```

|名稱|參數|主體|資料夾|DocString|
|---|---|---|---|---|
|TestFunction|（myLimit： int）|{StormEvents &#124; take myLimit}|MyFolder|使用參數的示範函式|
