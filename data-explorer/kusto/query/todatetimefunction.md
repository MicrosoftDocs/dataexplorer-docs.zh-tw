---
title: 日期時間() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的日期時間()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: abe3852195b1a79ab5c86176698099ed6e7ff7af
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506336"
---
# <a name="todatetime"></a>todatetime()

將輸入轉換為[日期時間](./scalar-data-types/datetime.md)標量。

```kusto
todatetime("2015-12-24") == datetime(2015-12-24)
```

**語法**

`todatetime(`*Expr*`)`

**引數**

* *Expr*: 將轉換為[日期時間的](./scalar-data-types/datetime.md)運算式 。 

**傳回**

如果轉換成功,結果將是[一個日期時間](./scalar-data-types/datetime.md)值。
如果轉換不成功,結果將為空。
 
*注意*:盡可能使用[日期時間()。](./scalar-data-types/datetime.md)