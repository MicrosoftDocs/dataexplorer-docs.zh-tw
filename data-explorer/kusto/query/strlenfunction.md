---
title: strlen() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 strlen()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 33bb1ea56ebc03dc7357264bcdf987c191478cbb
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506863"
---
# <a name="strlen"></a>strlen()

返回輸入字串的長度(以字元表示)。

**語法**

`strlen(`*源*`)`

**引數**

* *來源*:將測量字串長度的原始字串。

**傳回**

返回輸入字串的長度(以字元表示)。

**注意事項**

字串中的每個 Unicode 字元等`1`於 ,包括代理項。
(例如:儘管漢字在 UTF-8 編碼中需要多個值,但漢字將被計數一次)。


**範例**

```kusto
print length = strlen("hello")
```

|長度|
|---|
|5|

```kusto
print length = strlen("⒦⒰⒮⒯⒪")
```

|長度|
|---|
|5|