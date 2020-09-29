---
title: 啟用和停用具體化 view 命令-Azure 資料總管
description: 本文說明如何在 Azure 資料總管中啟用或停用具體化 view 命令。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/30/2020
ms.openlocfilehash: bb1fab3f211de4b33ca0dd2cee6a8cfa0cc796a9
ms.sourcegitcommit: 041272af91ebe53a5d573e9902594b09991aedf0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91452675"
---
# <a name="disable--enable-materialized-view"></a>.disable | .enable materialized-view

您可以使用下列任何一種方式停用具體化視圖：

* **系統自動停用：**  如果具體化因為永久錯誤而失敗，則會自動停用具體化 view。 此程式可能會發生在下列實例中： 
    * 與 view 定義不一致的架構變更。  
    * 對來源資料表所做的變更，造成具體化 view 查詢在語義上無效。 
* **明確停用具體化視圖：**  如果具體化視圖對叢集的健康狀態有負面影響 (例如，耗用太多 CPU) ，請使用下列 [命令](#syntax) 停用此視圖。

> [!NOTE]
> * 當具體化視圖停用時，具體化將會暫停，且不會使用叢集的資源。 即使在停用的情況下也可以查詢具體化視圖，但效能可能會很差。 已停用之具體化視圖的效能取決於已停用內嵌至來源資料表的記錄數目。 
> * 您可以啟用先前已停用的具體化視圖。 重新啟用時，具體化視圖將會從它所離開的點繼續具體化，且不會略過任何記錄。 如果停用此視圖很長一段時間，可能需要很長的時間才能趕上。

只有當您懷疑該觀點影響到叢集的健康狀態時，才建議停用 view。

## <a name="syntax"></a>Syntax

`.enable` | `disable``materialized-view` *MaterializedViewName*

## <a name="properties"></a>屬性

|屬性|類型|描述
|----------------|-------|---|
|MaterializedViewName|String|具體化視圖的名稱。|

## <a name="example"></a>範例

```kusto
.enable materialized-view ViewName

.disable materialized-view ViewName
```