---
title: 十進位資料類型 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的小數數據類型。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 62fd745c804ad4a50652e09cd722c17a4a61daac
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509889"
---
# <a name="the-decimal-data-type"></a>十進位資料類型

數據類型`decimal`表示 128 位寬的小數。

數據類型的`decimal`字面與 具有相同的表示形式。NET`System.Data.SqlTypes.SqlDecimal`的 。

`decimal(1.0)``decimal(0.1)`,並且`decimal(1e5)`都是類型`decimal`的 字面。

有幾種特殊的字面形式:
* `decimal(null)`: 是[null 值](null-values.md)。