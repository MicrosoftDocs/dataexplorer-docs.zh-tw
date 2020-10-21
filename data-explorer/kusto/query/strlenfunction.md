---
title: 'strlen ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 strlen ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4256218669685ec12a939156021803105e5ddfc6
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248409"
---
# <a name="strlen"></a>strlen()

傳回輸入字串的長度（以字元為單位）。

## <a name="syntax"></a>語法

`strlen(`*源*`)`

## <a name="arguments"></a>引數

* *來源*：將針對字串長度測量的來源字串。

## <a name="returns"></a>傳回

傳回輸入字串的長度（以字元為單位）。

**備註**

字串中的每個 Unicode 字元都等於 `1` ，包括代理程式。
 (例如：即使在 UTF-8 編碼) 中需要一個以上的值，也會計算中文字元。


## <a name="examples"></a>範例

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