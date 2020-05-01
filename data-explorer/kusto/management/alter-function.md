---
title: 。 alter function-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 alter function。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: d8248fdd9428df11a8e77316eec621102ddff9d6
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617794"
---
# <a name="alter-function"></a>.alter 函式

改變現有的函式，並將它儲存在資料庫中繼資料內。
參數類型和 CSL 語句的規則與 for [ `let`語句](../query/letstatement.md)相同。

**語法**

```kusto
.alter function [with (docstring = '<description>', folder='<name>', skipvalidation='true')] [FunctionName] ([paramName:paramType], ...) { CSL-statement }
```
    
|輸出參數 |類型 |描述
|---|---|--- 
|名稱  |String |函數的名稱。
|參數  |String |函數所需的參數。
|body  |String |（零或多個）`let`語句，後面接著在函式呼叫時評估的有效 CSL 運算式。
|資料夾|String|用於 UI 函數分類的資料夾。 這個參數不會變更函數的叫用方式。
|DocString|String|UI 用途的函式描述。

> [!NOTE]
> * 如果函數不存在，則會傳回錯誤。 如需建立新的函式，請參閱[. create function](create-function.md)
> * 需要[資料庫管理員許可權](../management/access-control/role-based-authorization.md)
> * 原先建立函數的[資料庫使用者](../management/access-control/role-based-authorization.md)可以修改函式。 
> * 語句中`let`不支援所有的 Kusto 類型。 支援的類型為： string、long、datetime、timespan 和 double。
> * 用`skipvalidation`來略過函數的語意驗證。 當函式以不正確的順序建立，而且先前已建立使用 F2 的 F1 時，這項功能就很有用。
 
**範例** 

```kusto
.alter function
with (docstring = 'Demo function with parameter', folder='MyFolder')
 MyFunction2(myLimit: long)  {StormEvents | limit myLimit}
``` 
    
|名稱 |參數 |body|資料夾|DocString
|---|---|---|---|---
|MyFunction2 |（myLimit： long）| {StormEvents &#124; 限制 myLimit}|MyFolder|使用參數的示範函式|
