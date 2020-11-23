---
title: 使用運算子-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的取用操作員。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 4b2a9d100cdb7125ebed21be69433fa3cee3a929
ms.sourcegitcommit: 4c7f20dfd59fb5b5b1adfbbcbc9b7da07df5e479
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2020
ms.locfileid: "95324528"
---
# <a name="consume-operator"></a>consume 運算子

使用傳遞給運算子的表格式資料流程。 

`consume`運算子大部分用來觸發查詢副作用，而不會實際將結果傳回給呼叫端。

```kusto
T | consume
```

## <a name="syntax"></a>Syntax

`consume` [ `decodeblocks` `=` *DecodeBlocks*]

## <a name="arguments"></a>引數

* *DecodeBlocks*：常數布林值。 如果設定為 `true` ，或如果要求屬性 `perftrace` 設定為 `true` ，則 `consume` 運算子不會只列舉其輸入的記錄，但實際上會強制將這些記錄中的每個值解壓縮和解碼。

`consume`操作員可以用來預估查詢成本，而不會實際將結果傳回給用戶端。
 (估計不會因各種原因而不精確;例如， `consume` 會計算 distributively，所以不會在叢集的 `T | consume` 節點之間傳送資料表的資料。 ) 
