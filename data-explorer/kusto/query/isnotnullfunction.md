---
title: 'isnotnull ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 isnotnull ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 44161dcdc34232225f4b133fe0a569fb818c3f0f
ms.sourcegitcommit: 455d902bad0aae3e3d72269798c754f51442270e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93349370"
---
# <a name="isnotnull"></a>isnotnull()

`true`如果引數不是 null，則會傳回。

## <a name="syntax"></a>語法

`isnotnull(`[ *值* ]`)`

`notnull(`[ *值* ] `)` -別名 `isnotnull`

## <a name="example"></a>範例

```kusto
T | where isnotnull(PossiblyNull) | count
```
