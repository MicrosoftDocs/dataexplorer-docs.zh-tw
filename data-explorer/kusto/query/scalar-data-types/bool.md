---
title: 布林資料類型 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的布爾數據類型。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: dd9cfa4f97f3baa4353f967f5e1ca31186f4815f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509923"
---
# <a name="the-bool-data-type"></a>布林資料類型

`bool` (`boolean`) 資料類型可以具有兩種狀態`true`之`false`一 : 或(`1``0`分別編碼為和 ))以及 null 值。

## <a name="bool-literals"></a>布林文字

資料類型`bool`具有以下文字:
* `true`與`bool(true)`: 代表真實
* `false`與`bool(false)`: 代表謊言
* `bool(null)`:請參閱[空值](null-values.md)

## <a name="bool-operators"></a>布林運算子

資料型`bool`態 支援以下運算子:相`==`等性 ( )、`!=`不等式`and`()、邏輯`or`與 ( ) 和邏輯或 ( 。