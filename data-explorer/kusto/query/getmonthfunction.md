---
title: getmonth （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 getmonth （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/22/2020
ms.openlocfilehash: 13254314bdbab7ddbdb74341e384867c26b6259d
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347692"
---
# <a name="getmonth"></a>getmonth()

從日期時間取得月份 (1-12)。

另一個別名： monthoyear （）

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print month = getmonth(datetime(2015-10-12))
```
