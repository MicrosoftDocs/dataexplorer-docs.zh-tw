---
title: drop function-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 drop function。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 279af228d15f511b35c26eebd0d21521450be786
ms.sourcegitcommit: 6f610cd9c56dbfaff4eb0470ac0d1441211ae52d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/12/2020
ms.locfileid: "91954734"
---
# <a name="drop-function"></a>.drop 函式

從資料庫卸載函數。
若要從資料庫中卸載多個函數，請參閱 [。 drop 函數](#drop-functions)。

**語法**

`.drop``function` *FunctionName* [ `ifexists` ]

* `ifexists`：如果有指定，則會針對不存在的函式將命令的行為修改為不會失敗。

> [!NOTE]
> * 需要 [function admin 許可權](../management/access-control/role-based-authorization.md)。
    
|輸出參數 |類型 |描述
|---|---|--- 
|Name  |String |已移除的函式名稱
 
**範例** 

```kusto
.drop function MyFunction1
```

## <a name="drop-functions"></a>. drop 函數

從資料庫卸載多個函數。

**語法**

`.drop``functions` (*FunctionName1*、 *FunctionName2*,。。) [ifexists]

**傳回**

此命令會傳回資料庫中其餘函數的清單。

|輸出參數 |類型 |描述
|---|---|--- 
|Name  |String |函式的名稱。 
|參數  |字串 |函數所需的參數。
|主體  |字串 | (零或多個) `let` 語句，後面接著在函式呼叫時評估的有效 CSL 運算式。
|資料夾|字串|用於 UI 函數分類的資料夾。 此參數不會變更叫用函數的方式。
|DocString|字串|適用于 UI 用途的函式描述。

需要 [function admin 許可權](../management/access-control/role-based-authorization.md)。

**範例** 
 
```kusto
.drop functions (Function1, Function2, Function3) ifexists
```
