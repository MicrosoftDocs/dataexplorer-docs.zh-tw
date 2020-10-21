---
title: 正則運算式-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的正則運算式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 1923b46aa7ec7176d2a41181326d32a2214f00c4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92244831"
---
# <a name="re2-syntax"></a>RE2 語法

RE2 正則運算式語法描述 Kusto (RE2) 所使用的正則運算式程式庫的語法。
Kusto 中有一些函數會使用正則運算式來執行字串比對、選取和解壓縮

- [countof()](countoffunction.md)
- [extract()](extractfunction.md)
- [extract_all()](extractallfunction.md)
- [符合 RegEx](datatypes-string-operators.md)
- [parse 運算子](parseoperator.md)
- [取代 ( # B1 ](replacefunction.md)
- [trim()](trimfunction.md)
- [trimend ( # B1 ](trimendfunction.md)
- [trimstart ( # B1 ](trimstartfunction.md)

Kusto 支援的正則運算式語法是 [re2 程式庫](https://github.com/google/re2/wiki/Syntax)的。 這些運算式必須在 Kusto 中以常值編碼 `string` ，而且所有 Kusto 的字串引號規則都適用。 例如，正則運算式 `\A` 會比對行的開頭，並在 Kusto 中指定為字串常值 `"\\A"` (請注意 () 字元) 的「額外」反斜線 `\` 。
