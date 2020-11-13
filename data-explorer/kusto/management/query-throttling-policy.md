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
ms.openlocfilehash: 0f50170e3ccfab20d434a90188baf511de056cef
ms.sourcegitcommit: 541164d8745022906b1da4aaca41ec15393f85d5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94570560"
---
# <a name="query-throttling-policy"></a>查詢節流原則

定義查詢節流原則，以限制可同時執行叢集的並行查詢數目。 此原則可保護叢集的並行查詢數目超過其可承受的數目。 您可以在執行時間變更原則，並在 alter policy 命令完成之後立即進行。

* 用 [`.show cluster policy querythrottling`](query-throttling-policy-commands.md#show-cluster-policy-querythrottling) 來顯示叢集目前的查詢節流原則。
* 用 [`.alter cluster policy querythrottling`](query-throttling-policy-commands.md#alter-cluster-policy-querythrottling) 來設定叢集目前的查詢節流原則。
* 用 [`.delete cluster policy querythrottling`](query-throttling-policy-commands.md#delete-cluster-policy-querythrottling) 來刪除叢集目前的查詢節流原則。

> [!NOTE]
> 請勿在沒有完整測試的情況下變更查詢節流原則。 如果叢集資源未依負載調整，則變更可能會導致效能降低，因此查詢可能會開始失敗。 
