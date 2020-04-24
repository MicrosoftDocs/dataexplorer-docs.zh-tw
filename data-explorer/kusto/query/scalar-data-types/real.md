---
title: 真實資料類型 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的實際數據類型。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: f69b74670fefcff6f6c24d5235f58beb98b0405a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509668"
---
# <a name="the-real-data-type"></a>真實資料類型

數據類型`real`表示 64 位寬、雙精度的浮點數。

數據類型的`real`字面與 具有相同的表示形式。NET`System.Double`的 。 `1.0``0.1`,並且`1e5`都是類型`real`的 字面。

有幾種特殊的字面形式:
* `real(null)`: 是[null 值](null-values.md)。
* `real(nan)`:非數位 (NaN)。 例如,除以`0.0`另一`0.0`個的結果。
* `real(+inf)`:正無窮大。 例如,除以`1.0``0.0`的結果。
* `real(-inf)`:負無窮大。 例如,除以`-1.0``0.0`的結果。