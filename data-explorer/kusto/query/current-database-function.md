---
title: 'current_database ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文描述 Azure 資料總管中 ( # A1 current_database。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b4e0458c78023d35e002910de4c5946260b43b4f
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252548"
---
# <a name="current_database"></a>current_database()

如果未指定) 的其他資料庫，則會在 (資料庫的範圍中，傳回所有查詢實體都會解析的資料庫名稱。

## <a name="syntax"></a>語法

`current_database()`

## <a name="returns"></a>傳回

範圍中的資料庫名稱，範圍為類型的值 `string` 。

## <a name="example"></a>範例

```kusto
print strcat("Database in scope: ", current_database())
```