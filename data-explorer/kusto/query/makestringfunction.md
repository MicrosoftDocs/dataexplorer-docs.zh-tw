---
title: 'make_string ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 make_string。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c8f3497e10c15bfd6df0337758d8dc3002419fa1
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252233"
---
# <a name="make_string"></a>make_string()

傳回 Unicode 字元產生的字串。
    
## <a name="syntax"></a>語法

`make_string (`*Arg1*[， *ArgN*] .。。 `)`

## <a name="arguments"></a>引數

* *Arg1* .。。 *ArgN*：整數 (int 或 long) 的運算式，或保存整數陣列的動態值。

* 此函數最多可接收64個引數。

## <a name="returns"></a>傳回

傳回 Unicode 字元所組成的字串，其點值是由這個函式的引數所提供。 輸入必須包含有效的 Unicode 字碼指標。
如果有任何引數未對應至 Unicode 字元，函數會傳回 `null` 。

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
