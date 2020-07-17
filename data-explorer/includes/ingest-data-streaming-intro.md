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
ms.openlocfilehash: e6704592a5b0f1a984ea5651eb8073ac105fda3d
ms.sourcegitcommit: 537a7eaf8c8e06a5bde57503fedd1c3706dd2b45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/16/2020
ms.locfileid: "86423118"
---
當您需要在內嵌和查詢之間進行低延遲時，請使用串流內嵌來載入資料。 串流內嵌作業會在10秒內完成，而且在完成後，您的資料會立即可供查詢。 此內嵌方法適用于內嵌大量的資料，例如每秒數千筆記錄，散佈于數千個數據表。 每個資料表都會收到相對較少的資料量，例如每秒幾筆記錄。

當資料內嵌數量超過每個資料表的每小時 4 GB 時，請使用大量內嵌，而不是串流內嵌。

若要深入瞭解不同的內嵌方法，請參閱[資料內嵌總覽](../ingest-data-overview.md)。
