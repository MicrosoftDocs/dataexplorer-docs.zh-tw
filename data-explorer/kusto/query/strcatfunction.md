---
title: strcat() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 strcat()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: dd01f875b45be038371cc184987aa2a8f8b8d5eb
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506914"
---
# <a name="strcat"></a>strcat()

在 1 到 64 個參數之間串聯。

* 如果參數不是字串類型,則它們將被強制轉換為字串。

**語法**

`strcat(`*參數1*,*參數2* [,*參數N*]`)`

**引數**

* *參數1* ...*參數N* :要串聯的運算式。

**傳回**

參數,串聯到單個字串。

**範例**
  
   ```kusto
print str = strcat("hello", " ", "world")
```

|字串|
|---|
|hello world|