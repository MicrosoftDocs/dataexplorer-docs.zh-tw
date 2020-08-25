---
title: 'dynamic_to_json ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 dynamic_to_json。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 08/05/2020
ms.openlocfilehash: 94901a69b4c4435e983f39f1692cf45cf27756e5
ms.sourcegitcommit: 05489ce5257c0052aee214a31562578b0ff403e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88793796"
---
# <a name="dynamic_to_json"></a>dynamic_to_json()

將 `dynamic` 輸入轉換成字串表示。
如果輸入是屬性包，輸出字串會以遞迴方式列印其內容（依按鍵排序）。 否則，輸出會類似于函式 `tostring` 輸出。

## <a name="syntax"></a>語法

`dynamic_to_json(Expr)`

## <a name="arguments"></a>引數

* *Expr*： `dynamic` 輸入。 函數會接受一個引數。

## <a name="returns"></a>傳回

傳回輸入的字串表示 `dynamic` 。 如果輸入是屬性包，輸出字串會以遞迴方式列印其內容（依按鍵排序）。

## <a name="examples"></a>範例

運算式：

```kusto
  let bag1 = dynamic_to_json(dynamic({ 'Y10':dynamic({ }), 'X8': dynamic({ 'c3':1, 'd8':5, 'a4':6 }),'D1':114, 'A1':12, 'B1':2, 'C1':3, 'A14':[15, 13, 18]}));
  print bag1
```
  
結果：

```
"{
  ""A1"": 12,
  ""A14"": [
    15,
    13,
    18
  ],
  ""B1"": 2,
  ""C1"": 3,
  ""D1"": 114,
  ""X8"": {
    ""c3"": 1,
    ""d8"": 5,
    ""a4"": 6
  },
  ""Y10"": {}
}"
```

運算式：

```kusto
 let bag2 = dynamic_to_json(dynamic({ 'X8': dynamic({ 'a4':6, 'c3':1, 'd8':5}), 'A14':[15, 13, 18], 'C1':3, 'B1':2, 'Y10': dynamic({ }), 'A1':12, 'D1':114}));
 print bag2
```
 
結果：

```
{
  "A1": 12,
  "A14": [
    15,
    13,
    18
  ],
  "B1": 2,
  "C1": 3,
  "D1": 114,
  "X8": {
    "a4": 6,
    "c3": 1,
    "d8": 5
  },
  "Y10": {}
}
```
