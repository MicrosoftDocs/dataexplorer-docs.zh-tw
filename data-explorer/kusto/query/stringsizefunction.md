---
title: string_size() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 string_size()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7886e7b8fd5039c9b197ae435bce4f40b93e5038
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506880"
---
# <a name="string_size"></a>string_size()

返回輸入字串的大小(以位元組為單位)。

**語法**

`string_size(`*源*`)`

**引數**

* *來源*:將測量字串大小的原始字串。

**傳回**

返回輸入字串的長度(以位元組為單位)。

**範例**

```kusto
print size = string_size("hello")
```

|size|
|---|
|5|

```kusto
print size = string_size("⒦⒰⒮⒯⒪")
```

|size|
|---|
|15|