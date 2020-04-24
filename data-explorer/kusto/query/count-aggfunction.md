---
title: 計數() (聚合函數) - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 count(聚合函數)。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: 59b898d44507d844db1f714ef15effa004e5546f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516995"
---
# <a name="count-aggregation-function"></a>計數()(聚合函數)

返回每個匯總組記錄的計數(或者,如果總結是在未分組的情況下完成的)。

* 只能在[匯總](summarizeoperator.md)的聚合上下文中使用
* 使用[countif](countif-aggfunction.md)聚合函數僅計算某些謂`true`詞返回的記錄。

**語法**

總結`count()`

**傳回**

返回每個匯總組記錄的計數(或者,如果總結是在未分組的情況下完成的)。