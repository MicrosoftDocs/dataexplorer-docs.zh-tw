---
title: strcat_delim() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 azure 數據資源管理器中的strcat_delim()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f944af741cd5f655c2c9b090ddebc6cc35a47766
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506931"
---
# <a name="strcat_delim"></a>strcat_delim()

在 2 到 64 個參數之間串聯,與分隔符作為第一個參數提供。

 * 如果參數不是字串類型,則它們將被強制轉換為字串。

**語法**

`strcat_delim(`*分隔符*,*參數1*,*參數2* [,*參數N*]`)`

**引數**

* *分隔*:字串表示式,它將用作分隔符。
* *參數1* ...*參數N* :要串聯的運算式。

**傳回**

參數,與分隔*串*聯到單個字串。

**範例**

```kusto
print st = strcat_delim('-', 1, '2', 'A', 1s)

```

|st|
|---|
|1-2-A-00:00:01|