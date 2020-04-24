---
title: 使用 ODBC 連線到 Azure 資料資源管理員
description: 在本文中,您將瞭解如何設置與 Azure 數據資源管理員的開放資料庫連接 (ODBC) 連接。
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: 156257613195cb53730273ac3a654908feac9601
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81496611"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>使用 ODBC 連線到 Azure 資料資源管理員

開放資料庫連線 ([ODBC](/sql/odbc/reference/odbc-overview)) 是一個被廣泛接受的應用程式程式設計介面 (API) 用於資料庫存取. 使用 ODBC 從沒有專用連接器的應用程式連接到 Azure 資料資源管理器。

在背景,應用程式呼叫 ODBC 介面中的函數,這些函式在資料庫特定的模組中,為*驅動程式*。 Azure 資料資源管理員支援 SQL Server 通訊協定的子集[(MS-TDS),](kusto/api/tds/index.md)因此可以使用 SQL Server 的 ODBC 驅動程式。

使用以下視頻,您可以學習創建 ODBC 連接。 

> [!VIDEO https://www.youtube.com/embed/qA5wxhrOwog]

或者,您可以按照下文詳述的[設定 ODBC 資料來源](#configure-the-odbc-data-source)。 

在本文中,您將瞭解如何使用 SQL Server ODBC 驅動程式,以便可以從支援 ODBC 的任何應用程式連接到 Azure 資料資源管理器。 

## <a name="prerequisites"></a>Prerequisites

您需要下列項目：

* [適用於 SQL Server 版本 17.2.0.1 或更高版本的](/sql/connect/odbc/download-odbc-driver-for-sql-server)作業系統的 Microsoft ODBC 驅動程式。

## <a name="configure-the-odbc-data-source"></a>設定 ODBC 資料來源

按照以下步驟使用 SQL Server 的 ODBC 驅動程式配置 ODBC 資料來源。

1. 在 Windows 中,搜索*ODBC 資料來源*,並打開 ODBC 資料來源桌面應用。

1. 選取 [新增]  。

    ![新增資料來源](media/connect-odbc/add-data-source.png)

1. 為 SQL 伺服器選擇**ODBC 驅動程式 17,** 然後**完成**。

    ![選擇驅動程式](media/connect-odbc/select-driver.png)

1. 輸入要連接到的連接和群集的名稱和說明,然後選擇 **「下一步**」。 叢集網址 應位於*\<群集\>名稱\<表單中。區域\>.kusto.windows.net*。

    ![選取伺服器](media/connect-odbc/select-server.png)

1. 選擇**作用目錄整合**,然後**下一個**。

    ![Active Directory 整合式](media/connect-odbc/active-directory-integrated.png)

1. 選擇具有範例資料的資料庫,然後**選擇 Next**。

    ![變更預設資料庫](media/connect-odbc/change-default-database.png)

1. 在下一個螢幕上,將所有選項保留為預設值,然後選擇 **"完成**"。

1. 測試**資料來源**。

    ![測試資料來源](media/connect-odbc/test-data-source.png)

1. 驗證測試是否成功,然後選擇 **「確定**」。 如果測試未成功,請檢查您在前面的步驟中指定的值,並確保您有足夠的許可權連接到群集。

    ![測試成功](media/connect-odbc/test-succeeded.png)

## <a name="next-steps"></a>後續步驟

* [從 Tableau 連線到 Azure 資料資源管理員](tableau.md)