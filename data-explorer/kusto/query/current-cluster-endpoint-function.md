---
title: 'current_cluster_endpoint ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文描述 Azure 資料總管中 ( # A1 current_cluster_endpoint。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: cfd35837b0c1affbb2101e6c71a4fa638ef8e466
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252562"
---
# <a name="current_cluster_endpoint"></a>current_cluster_endpoint()

傳回目前所查詢之叢集 (DNS 名稱) 的網路端點。

## <a name="syntax"></a>語法

`current_cluster_endpoint()`

## <a name="returns"></a>傳回

要查詢的目前叢集 (DNS 名稱) 的網路端點，作為類型的值 `string` 。

## <a name="example"></a>範例

```kusto
print strcat("This query executed on: ", current_cluster_endpoint())
```