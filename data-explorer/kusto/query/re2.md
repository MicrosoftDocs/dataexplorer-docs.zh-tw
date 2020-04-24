---
title: 正規表示式 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的正則運算式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 0bc5dc6705d6cada446716f2f9d84618322ecd76
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510518"
---
# <a name="regular-expressions"></a>規則運算式

RE2 正則表示式語法描述 Kusto (re2) 使用的正則運算式庫的語法。
Kusto 中有幾個函式使用正規表示式執行字串符合、選擇和擷取

- [countof()](countoffunction.md)
- [擷取物()](extractfunction.md)
- [extract_all()](extractallfunction.md)
- [符合 RegEx](datatypes-string-operators.md)
- [parse 運算子](parseoperator.md)
- [取代()](replacefunction.md)
- [修剪()](trimfunction.md)
- [飾件()](trimendfunction.md)
- [修剪啟動()](trimstartfunction.md)

Kusto 支援的正則表達式語法是[re2 庫](https://github.com/google/re2/wiki/Syntax)的正則表達式語法,下面詳細介紹了該語法。 請注意,這些運算式必須在 Kusto 中編`string`碼為文本,並且所有 Kusto 的字串報價規則都適用。 例如,正則表達式`\A`與行的開頭匹配(見下表),並在 Kusto 中指定為字`"\\A"`串文本 (注意"額外"反`\`斜杠 ( ) 字元。

