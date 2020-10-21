---
title: 邏輯 (二元) 運算子-Azure 資料總管 |Microsoft Docs
description: 本文描述 Azure 資料總管中的邏輯 (二元) 操作員。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/14/2018
ms.openlocfilehash: 6d1fcf7b9786951fedbdbf45d9e2c20a765452dd
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248515"
---
# <a name="logical-binary-operators"></a>邏輯 (二元) 運算子

以下是兩個型別值之間支援的邏輯運算子 `bool` ：

> [!NOTE]
> 這些邏輯運算子有時稱為布林值運算子，有時也稱為二元運算子。 這些名稱都是同義字。

|操作員名稱|語法|意義|
|-------------|------|-------|
|等式     |`==`  |`true`如果兩個運算元都不是 null 且彼此相等，則會產生。 否則為 `false`。|
|不等於   |`!=`  |`true`如果兩個運算元的其中一個 (或兩個) 都是 null，或兩者都不相等，則會產生。 否則為 `true`。|
|邏輯 and  |`and` |`true`如果兩個運算元都是，則會產生 `true` 。|
|邏輯 or   |`or`  |`true`如果其中一個運算元是，則會產生 `true` ，而不考慮其他運算元。|

> [!NOTE]
> 由於布林值 null 值的行為 `bool(null)` ，因此兩個布林值 null 值不等於或不等於 (換句話說， `bool(null) == bool(null)` 而且 `bool(null) != bool(null)` 兩者都會產生值 `false`) 。
>
> 另一方面，將 `and` / `or` null 值視為相當於 `false` ， `bool(null) or true` 而是 `true` ， `bool(null) and true` 則為 `false` 。