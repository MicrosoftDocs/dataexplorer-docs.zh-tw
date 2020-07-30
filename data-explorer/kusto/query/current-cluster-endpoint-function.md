---
title: current_cluster_endpoint （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 current_cluster_endpoint （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2c3ddbee55e729ae8afbb6c1fbcc213bd6bfd9ce
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348712"
---
# <a name="current_cluster_endpoint"></a>current_cluster_endpoint()

傳回目前正在查詢之叢集的網路端點（DNS 名稱）。

## <a name="syntax"></a>語法

`current_cluster_endpoint()`

## <a name="returns"></a>傳回

目前正在查詢之叢集的網路端點（DNS 名稱），其為類型的值 `string` 。

## <a name="example"></a>範例

```kusto
print strcat("This query executed on: ", current_cluster_endpoint())
```