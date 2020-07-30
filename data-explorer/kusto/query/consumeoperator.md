---
title: 使用運算子-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的取用運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 85fd891590e359e31224ed5d707a837b1cc0eb41
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348831"
---
# <a name="consume-operator"></a>consume 運算子

使用傳遞給運算子的表格式資料流程。 

`consume`運算子大多用於觸發查詢副作用，而不會實際將結果傳回給呼叫者。

```kusto
T | consume
```

## <a name="syntax"></a>語法

`consume`[ `decodeblocks` `=` *DecodeBlocks*]

## <a name="arguments"></a>引數

* *DecodeBlocks*：常數布林值。 如果設定為 `true` ，或如果要求屬性 `perftrace` 設定為 `true` ，則 `consume` 運算子不會只列舉其輸入的記錄，但實際上會強制將這些記錄中的每個值解壓縮和解碼。

`consume`運算子可用於估計查詢的成本，而不會實際將結果傳遞回用戶端。
（估計不會因各種原因而精確; 例如， `consume` 是計算 distributively，因此不會在叢集的 `T | consume` 節點之間傳送資料表的資料）。

<!--
* *WithStats*: A constant Boolean value. If set to `true` (or if the global
  property `perftrace` is set), the operator will return a single
  row with a single column called `Stats` of type `dynamic` holding the statistics
  of the data source fed to the `consume` operator.
-->