---
title: 使用匯入到 Microsoft Excel 的 Azure 資料資源管理員庫斯托查詢視覺化資料
description: 在本文中,您將瞭解如何將 Azure 資料資源管理器 Kusto 查詢導入 Microsoft Excel。
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 4999000e2084922b43b8085034f545d4b5c644a9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81499523"
---
# <a name="visualize-data-using-an-azure-data-explorer-kusto-query-imported-into-microsoft-excel"></a>使用匯入到 Microsoft Excel 的 Azure 資料資源管理員庫斯托查詢視覺化資料

Azure 資料資源管理員提供了兩個用於連接到 Excel 中的數據的選項:使用本機連接器或從 Azure 數據資源管理器導入查詢。 本文介紹如何將查詢從 Azure 資料資源管理員導入 Excel 以可視化數據。 將 Kusto 查詢添加為 Excel 資料來源,以便對資料執行其他計算或可視化。

## <a name="prerequisites"></a>Prerequisites

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* 屬於 Azure 的目錄的組織電子郵件帳戶,因此您可以連接到[Azure 資料資源管理員說明叢集](https://dataexplorer.azure.com/clusters/help/databases/Samples) 
<br>或</br>
* [建立測試叢集與資料庫](create-cluster-database-portal.md)並登入 Azure[資料資源管理員 WebUI 應用程式](https://dataexplorer.azure.com/)。

## <a name="define-kusto-query-as-an-excel-data-source"></a>將 Kusto 查詢定義為 Excel 資料來源

1. 在[Azure 資料資源管理器 Web UI](https://dataexplorer.azure.com/clusters/help/databases/Samples)中,運行查詢並檢查結果。

1. 選擇「**共用**」選項卡,然後選擇 **「查詢以通電 BI」。。**

    ![Web UI 查詢到 Power BI](media/excel-blank-query/web-ui-query-to-powerbi.png)

1. 顯示一個視窗,其中顯示以下通知:

    ![將查詢匯出到剪貼簿](media/excel-blank-query/query-exported-to-clipboard.png)

1. 開啟**Microsoft Excel**。

1. 在「**資料**」選項卡中,選擇**從其他來源** > **取得資料** > **為空查詢**。

    ![取得資料並選擇空白查詢](media/excel-blank-query/get-data-blank-query.png)

1. 將打開 **「電源查詢編輯器」** 視窗。 在視窗中,選擇**進階編輯器**。

    ![電源查詢編輯器視窗](media/excel-blank-query/power-query-editor.png)

1. 在 **「進階編輯器」** 視窗中,貼上匯出到剪貼簿的查詢,然後選擇 **「完成**」 。

    ![進階編輯器查詢](media/excel-blank-query/advanced-editor-query.png)    

1. 要進行身份驗證,請選擇 **「編輯憑據**」。。

    ![編輯認證](media/excel-blank-query/edit-credentials.png)

1. 選擇**組織帳號**並**登入**。 完成登入過程,然後選擇「**連接**」。

    ![完成登入](media/excel-blank-query/complete-sign-in.png)

    重複上述步驟以添加更多查詢。 您可以將查詢重新命名為更有意義的名稱。

1. 選擇「**關閉& 載入**」按鈕,將資料放入 Excel。

    ![選擇關閉與載入](media/excel-blank-query/close-and-load.png)

1. 現在您的數據在 Excel 中。 選擇 **「刷新**」按鈕以刷新查詢。

    ![在 Excel 中檢視資料](media/excel-blank-query/data-in-excel.png)

## <a name="next-steps"></a>後續步驟

[使用 Excel 的 Azure 資料資源管理員連接器視覺化資料](excel-connector.md)