---
title: 正則運算式-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的正則運算式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 3bbd14031adbfee3b5fac07194f5ff879ff33693
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373064"
---
# <a name="regular-expressions"></a>規則運算式

RE2 正則運算式語法描述 Kusto （RE2）所使用之正則運算式程式庫的語法。
Kusto 中有幾個函式會使用正則運算式執行字串比對、選取和解壓縮

- [countof()](countoffunction.md)
- [extract()](extractfunction.md)
- [extract_all()](extractallfunction.md)
- [符合 RegEx](datatypes-string-operators.md)
- [parse 運算子](parseoperator.md)
- [replace （）](replacefunction.md)
- [trim （）](trimfunction.md)
- [trimend （）](trimendfunction.md)
- [trimstart （）](trimstartfunction.md)

Kusto 支援的正則運算式語法就是 re2 連結[庫](https://github.com/google/re2/wiki/Syntax)的。 這些運算式必須在 Kusto 中編碼為 `string` 常值，而且所有 Kusto 的字串引號規則都適用。 例如，正則運算式 `\A` 會比對行首，並在 Kusto 中指定為字串常值 `"\\A"` （請注意 "額外的反斜線（ `\` ）字元）。
