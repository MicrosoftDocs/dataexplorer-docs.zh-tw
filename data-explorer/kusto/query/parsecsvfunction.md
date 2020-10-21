---
title: 'parse_csv ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 parse_csv。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1b0f1a0279dcb35e4e196b0f2170f4a64d58eca9
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246113"
---
# <a name="parse_csv"></a>parse_csv()

分割指定的字串，表示逗點分隔值的單一記錄，並傳回含有這些值的字串陣列。

```kusto
parse_csv("aaa,bbb,ccc") == ["aaa","bbb","ccc"]
```

## <a name="syntax"></a>語法

`parse_csv(`*源*`)`

## <a name="arguments"></a>引數

* *來源*：代表逗點分隔值之單一記錄的來源字串。

## <a name="returns"></a>傳回

包含分割值的字串陣列。

**備註**

內嵌的換行字元、逗號和引號可以使用雙引號 ( ' "' ) 。 此函數不支援每個資料列有多筆記錄 (只) 取得第一筆記錄。

## <a name="examples"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print result=parse_csv('aa,"b,b,b",cc,"Escaping quotes: ""Title""","line1\nline2"')
```

|result|
|---|
|[<br>  "aa"、<br>  "b，b，b"，<br>  "cc"、<br>  「引號： \" Title」 \" 、<br>  "line1\nline2"<br>]|

具有多個記錄的 CSV 承載：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print result_multi_record=parse_csv('record1,a,b,c\nrecord2,x,y,z')
```

|result_multi_record|
|---|
|[<br>  "record1"、<br>  "a"、<br>  "b"、<br>  "c"<br>]|
