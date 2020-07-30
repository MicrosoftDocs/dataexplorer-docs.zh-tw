---
title: make_string （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 make_string （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 36d31e88a89f23006dac73b92777b13db4933d06
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346876"
---
# <a name="make_string"></a>make_string()

傳回 Unicode 字元所產生的字串。
    
## <a name="syntax"></a>語法

`make_string (`*Arg1*[， *...argn*] .。。`)`

## <a name="arguments"></a>引數

* *Arg1* .。。*...Argn*：是整數（int 或 long）的運算式，或包含整數陣列的動態值。

* 此函式最多可接收64個引數。

## <a name="returns"></a>傳回

傳回 Unicode 字元的字串，其值是由這個函數的引數所提供。 輸入必須包含有效的 Unicode 字碼指標。
如果有任何引數未對應至 Unicode 字元，則函式會傳回 `null` 。

## <a name="examples"></a>範例

```kusto
print str = make_string(75, 117, 115, 116, 111)
```

|字串|
|---|
|Kusto|

```kusto
print str = make_string(dynamic([75, 117, 115, 116, 111]))
```

|字串|
|---|
|Kusto|

```kusto
print str = make_string(dynamic([75, 117, 115]), 116, 111)
```

|字串|
|---|
|Kusto|

```kusto
print str = make_string(75, 10, 117, 10, 115, 10, 116, 10, 111)
```

|字串|
|---|
|K<br>u<br>s<br>t<br>o|
