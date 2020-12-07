---
title: 系統資訊-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的系統資訊。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/07/2019
ms.openlocfilehash: 22ad4956d94f445b26e49e1e73ed54e86568d1ad
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321211"
---
# <a name="system-information"></a>系統資訊

本節摘要說明可供 `Database Admins` 和 `Database Monitors` 角色探索使用狀況、追蹤作業和調查內嵌失敗的命令。

* [`.show queries`](queries.md) -顯示已完成和執行中查詢的資訊。
* [`.show commands`](commands.md) -顯示已完成命令及其資源使用率的資訊。
* [`.show commands-and-queries`](commands-and-queries.md) -顯示已完成命令和查詢的相關資訊，以及它們的資源使用率。
* [`.show journal`](journal.md) -顯示中繼資料作業的歷程記錄。
* [`.show operations`](operations.md) -顯示執行中和已完成的管理作業，因為最後一次選擇管理節點。
* [`.show failed ingestions`](ingestionfailures.md) -顯示將資料內嵌到叢集時發生的失敗資訊。