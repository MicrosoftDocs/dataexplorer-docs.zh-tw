---
title: 包含檔案
description: 包含檔案
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 07/13/2020
ms.author: orspodek
ms.reviewer: alexefro
ms.custom: include file
ms.openlocfilehash: a748332c85839bac8466532ba3959810a6387834
ms.sourcegitcommit: 537a7eaf8c8e06a5bde57503fedd1c3706dd2b45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/16/2020
ms.locfileid: "86423047"
---
## <a name="disable-streaming-ingestion-on-your-cluster"></a>停用叢集上的串流內嵌

> [!WARNING]
> 停用串流內嵌可能需要幾個小時的時間。

在您的 Azure 資料總管叢集上停用串流內嵌之前，請先從所有相關的資料表和資料庫中卸載[串流內嵌原則](../kusto/management/streamingingestionpolicy.md)。 移除串流內嵌原則會在您的 Azure 資料總管叢集中觸發資料重新排列。 在資料行存放區（範圍或分區）中，從初始儲存體移到永久儲存體的串流內嵌資料。 視初始儲存體中的資料量而定，此程式可能需要幾秒鐘到幾個小時的時間。
