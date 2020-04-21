---
title: 微軟邏輯應用和庫塞托 - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 Microsoft 邏輯應用和庫托。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: f7d719ece5df6eb3f6d4060a2fb07e7092902601
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523812"
---
# <a name="microsoft-logic-app-and-kusto"></a>微軟邏輯應用程式和庫托

Azure Kusto 邏輯應用連接器允許使用者使用[Microsoft 邏輯應用](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps)作為計畫或觸發任務的一部分自動執行 Kusto 查詢和命令。

邏輯應用程式和流連接器構建在同一連接器之上,因此,如[Flow 文件頁](flow.md)中所述,相同的[限制](flow.md#limitations)、[操作](flow.md#azure-kusto-flow-actions),[身份驗證](flow.md#authentication)[和使用範例](flow.md#usage-examples)適用於這兩個。


## <a name="how-to-create-a-logic-app-with-azure-kusto"></a>如何使用 Azure 函式庫建立邏輯應用

打開 Azure 門戶,然後單擊創建新的邏輯應用資源。
添加所需的名稱、訂閱、重新創建組和位置,然後單擊"創建"。

![建立邏輯應用程式](./Images/KustoTools-LogicApp/logicapp-createlogicapp.png "邏輯應用-建立邏輯應用")

建立邏輯應用後,按下編輯按鈕

![編輯邏輯應用設計器](./Images/KustoTools-LogicApp/logicapp-editdesigner.png "邏輯應用編輯設計器")

建立空白邏輯應用

![邏輯應用空白範本](./Images/KustoTools-LogicApp/logicapp-blanktemplate.png "邏輯應用空白範本")

新增重複操作,然後選擇"Azure 庫塞托"

![邏輯應用 Kusto 串流連接器](./Images/KustoTools-LogicApp/logicapp-kustoconnector.png "邏輯應用-庫斯托連接器")