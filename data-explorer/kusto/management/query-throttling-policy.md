---
title: 查詢節流原則-Azure 資料總管
description: 本文說明 Azure 中的查詢節流原則資料總管
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: miwalia
ms.service: data-explorer
ms.topic: reference
ms.date: 10/05/2020
ms.openlocfilehash: 7d6db9ba7cbb7b1fbaaee7325043fed287673178
ms.sourcegitcommit: acd8aba5ed957bee4a7361057387145f9d3ef4e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91733994"
---
# <a name="query-throttling-policy"></a>查詢節流原則

定義查詢節流原則，以限制叢集同時執行的並行查詢數量。 您可以在執行時間變更原則，並在 alter policy 命令完成之後立即進行。

* 用 [`.show cluster policy querythrottling`](query-throttling-policy-commands.md#show-cluster-policy-querythrottling) 來顯示叢集目前的查詢節流原則。
* 用 [`.alter cluster policy querythrottling`](query-throttling-policy-commands.md#alter-cluster-policy-querythrottling) 來設定叢集目前的查詢節流原則。
* 用 [`.delete cluster policy querythrottling`](query-throttling-policy-commands.md#delete-cluster-policy-querythrottling) 來刪除叢集目前的查詢節流原則。

> [!NOTE]
> 如果您未定義查詢節流原則，大量並行查詢可能會導致叢集 inaccessibility 或效能降低。
