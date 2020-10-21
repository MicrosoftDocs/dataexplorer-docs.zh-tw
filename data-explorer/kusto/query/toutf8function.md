---
title: 'to_utf8 ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文描述 Azure 資料總管中 ( # A1 to_utf8。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c47c37dbbde7bd2276f1b5788dc6eb7b062f39a3
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251273"
---
# <a name="to_utf8"></a>to_utf8()

傳回輸入字串的 unicode 字元的動態陣列， (make_string) 的反向操作。

## <a name="syntax"></a>語法

`to_utf8(`*源*`)`

## <a name="arguments"></a>引數

* *來源*：要轉換的來源字串。

## <a name="returns"></a>傳回

傳回 unicode 字元的動態陣列，這些字元組成提供給此函數的字串。
請參閱 [`make_string()`](makestringfunction.md)) 

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
|[1511、1493、1505、1496、1493、32、45、32、75、117、115、116、111]|

```kusto
print str = make_string(to_utf8("Kusto"))
```

|字串|
|---|
|Kusto|
