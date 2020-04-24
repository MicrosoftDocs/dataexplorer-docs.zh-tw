---
title: 使用運算子 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的使用運算符。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 65c2f2befc074042131b5c0d705fa942a1622035
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517114"
---
# <a name="consume-operator"></a>consume 運算子

使用交給操作員的表格數據流。 

運算符`consume`主要用於觸發查詢副作用,而不實際將結果返回給調用方。

```kusto
T | consume
```

**語法**

`consume`【`decodeblocks` `=` *解碼塊*】

**引數**

* *解碼塊*:恆定的布爾值。 如果設定為`true`,或者請求`perftrace`屬性`true`設置`consume`為 , 則運算符將不僅在其輸入中枚舉記錄,而且實際強制對這些記錄中的每個值進行解壓縮和解碼。

運算元`consume`可用於估計查詢的成本,而無需實際將結果返回用戶端。
(由於各種原因,估計不精確;例如,`consume`是按分佈計算的,`T | consume`因此不會在群集的節點之間傳輸表的數據。

<!--
* *WithStats*: A constant Boolean value. If set to `true` (or if the global
  property `perftrace` is set), the operator will return a single
  row with a single column called `Stats` of type `dynamic` holding the statistics
  of the data source fed to the `consume` operator.
-->