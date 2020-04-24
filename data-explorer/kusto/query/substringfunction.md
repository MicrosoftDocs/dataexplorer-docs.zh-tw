---
title: 子字串() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的子字串()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b0273b3e93c8778af9c380f164faec74349aa8cd
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506693"
---
# <a name="substring"></a>substring()

從源字串中提取子字串,從某些索引開始到字串的末尾。

(選擇性) 您可以指定所要求子字串的長度。

```kusto
substring("abcdefg", 1, 2) == "bc"
```

**語法**

`substring(`*源*`,`*起始索引*`,`=*長度*|`)`

**引數**

* *來源*:子字串將從中獲取的源字串。
* *起始索引*:請求子字串的零起始字元位置。
* *長度*:可用於指定子字串中請求的字元數的可選參數。 

**注意事項**

*起始索引*可以是負數,在這種情況下,將從源字串的末尾檢索子字串。

**傳回**

指定字串中的子字串。 子字串開始於 startingIndex (以零為基礎的) 字元位置，並延續到字串結尾或長度字元 (如果有指定)。

**範例**

```kusto
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
substring("123456", -2, 2)    // 56
```