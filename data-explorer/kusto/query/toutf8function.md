---
title: to_utf8 （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 to_utf8 （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 891a2bb079136d9a7c21c1992b79e3e0eab4c970
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350667"
---
# <a name="to_utf8"></a>to_utf8()

傳回輸入字串之 unicode 字元的動態陣列（make_string 的反向運算）。

## <a name="syntax"></a>語法

`to_utf8(`*來源*`)`

## <a name="arguments"></a>引數

* *來源*：要轉換的來源字串。

## <a name="returns"></a>傳回

傳回 unicode 字元的動態陣列，其中組成提供給此函式的字串。
請參閱 [`make_string()`](makestringfunction.md) ）

## <a name="examples"></a>範例

```kusto
print arr = to_utf8("⒦⒰⒮⒯⒪")
```

|arr|
|---|
|[9382、9392、9390、9391、9386]|

```kusto
print arr = to_utf8("קוסטו - Kusto")
```

|arr|
|---|
|[1511，1493，1505，1496，1493，32，45，32，75，117，115，116，111]|

```kusto
print str = make_string(to_utf8("Kusto"))
```

|字串|
|---|
|Kusto|
