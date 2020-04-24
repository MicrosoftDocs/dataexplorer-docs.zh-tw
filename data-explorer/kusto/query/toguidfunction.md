---
title: 到guid() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 toguid()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fb1df5fe91b75e5d3b7d1a9f40b8b3079cac9944
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506132"
---
# <a name="toguid"></a>toguid()

將輸入轉換為[`guid`](./scalar-data-types/guid.md)表示形式。

```kusto
toguid("70fc66f7-8279-44fc-9092-d364d70fce44") == guid("70fc66f7-8279-44fc-9092-d364d70fce44")
```

**語法**

`toguid(`*Expr*`)`

**引數**

* *Expr*: 將[`guid`](./scalar-data-types/guid.md)轉換為 標量的運算式。 

**傳回**

如果轉換成功,結果將是一個[`guid`](./scalar-data-types/guid.md)標量。
如果轉換不成功,結果會為`null`。

*注意*:如果可能,最好使用[guid()。](./scalar-data-types/guid.md)