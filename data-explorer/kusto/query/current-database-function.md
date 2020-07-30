---
title: current_database （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 current_database （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d68c35547c840cc1e16224c376e90dfabec296d7
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348695"
---
# <a name="current_database"></a>current_database()

傳回範圍中的資料庫名稱（如果未指定其他資料庫，則會解析所有查詢實體的資料庫）。

## <a name="syntax"></a>語法

`current_database()`

## <a name="returns"></a>傳回

範圍中的資料庫名稱，其值為類型 `string` 。

## <a name="example"></a>範例

```kusto
print strcat("Database in scope: ", current_database())
```