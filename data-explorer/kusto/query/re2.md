---
title: 規則運算式 - Azure 資料總管 | Microsoft Docs
description: 本文說明 Azure 資料總管中的規則運算式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.localizationpriority: high
ms.openlocfilehash: e4dda7ca499ac9fc9f90f6576758797d3f62a299
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513075"
---
# <a name="re2-syntax"></a>RE2 語法

RE2 規則運算式語法會描述 Kusto (RE2) 所使用的規則運算式程式庫語法。
Kusto 中有幾個函式會使用規則運算式執行字串比對、選取和擷取

- [countof()](countoffunction.md)
- [extract()](extractfunction.md)
- [extract_all()](extractallfunction.md)
- [符合 RegEx](datatypes-string-operators.md)
- [parse 運算子](parseoperator.md)
- [replace()](replacefunction.md)
- [trim()](trimfunction.md)
- [trimend()](trimendfunction.md)
- [trimstart()](trimstartfunction.md)

Kusto 支援的規則運算式語法是 [re2 程式庫](https://github.com/google/re2/wiki/Syntax)的語法。 這些運算式必須以 `string` 常值的形式在 Kusto 中編碼，並且套用所有 Kusto 的字串引號規則。 例如，規則運算式 `\A` 會比對行首，並在 Kusto 中指定為字串常值 `"\\A"` (請注意「額外」的反斜線(`\`) 字元)。
