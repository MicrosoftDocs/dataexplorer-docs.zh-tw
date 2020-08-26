---
title: 使用 ODBC 連接到 Azure 資料總管
description: 在本文中，您將瞭解如何設定開放式資料庫連線 (ODBC) 連接至 Azure 資料總管。
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: how-to
ms.date: 06/30/2019
ms.openlocfilehash: ee5b898b5e4bbb72ad1cd32fcfb40ba0d144c02d
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88872993"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>使用 ODBC 連接到 Azure 資料總管

Open Database Connectivity ([ODBC](/sql/odbc/reference/odbc-overview)) 是廣泛接受的應用程式開發介面， (API) 以進行資料庫存取。 使用 ODBC 從沒有專用連線器的應用程式連接至 Azure 資料總管。

應用程式會在幕後呼叫 ODBC 介面中的函式，這些函式會在稱為 *驅動程式*的資料庫特定模組中執行。 Azure 資料總管支援 SQL Server 通訊協定的子集 ([ms-chap](kusto/api/tds/index.md)) ，因此可以使用 ODBC driver for SQL Server。

您可以使用下列影片，瞭解如何建立 ODBC 連接。 

> [!VIDEO https://www.youtube.com/embed/qA5wxhrOwog]

或者，您可以 [設定 ODBC 資料來源](#configure-the-odbc-data-source) ，如下所述。 

在本文中，您將瞭解如何使用 SQL Server ODBC 驅動程式，讓您可以從任何支援 ODBC 的應用程式連接到 Azure 資料總管。 

## <a name="prerequisites"></a>先決條件

您需要下列項目：

* 適用于您作業系統的[Microsoft ODBC Driver for SQL Server 17.2.0.1 版或更新版本](/sql/connect/odbc/download-odbc-driver-for-sql-server)。

## <a name="configure-the-odbc-data-source"></a>設定 ODBC 資料來源

請遵循下列步驟，使用 ODBC driver for SQL Server 設定 ODBC 資料來源。

1. 在 Windows 中，搜尋 *Odbc 資料來源*，然後開啟 Odbc 資料來源桌面應用程式。

1. 選取 [新增]。

    ![新增資料來源](media/connect-odbc/add-data-source.png)

1. **針對 SQL Server 選取 [ODBC 驅動程式 17]，** 然後**完成**。

    ![選取驅動程式](media/connect-odbc/select-driver.png)

1. 輸入連線的名稱和描述，以及您想要連線的叢集，然後選取 **[下一步]**。 叢集 URL 的格式應為* \<ClusterName\> \<Region\> 。kusto.windows.net*。

    ![選取伺服器](media/connect-odbc/select-server.png)

1. 依序選取 [ **Active Directory 整合** **]**。

    ![Active Directory 整合式](media/connect-odbc/active-directory-integrated.png)

1. 選取具有範例資料的資料庫，然後按一下 [ **下一步]**。

    ![變更預設資料庫](media/connect-odbc/change-default-database.png)

1. 在下一個畫面上，將所有選項保留為預設值，然後選取 **[完成]**。

1. 選取 [ **測試資料來源**]。

    ![測試資料來源](media/connect-odbc/test-data-source.png)

1. 確認測試成功，然後選取 **[確定]**。 如果測試失敗，請檢查您在先前步驟中指定的值，並確定您有足夠的許可權可連接到叢集。

    ![測試成功](media/connect-odbc/test-succeeded.png)

## <a name="next-steps"></a>後續步驟

* [從 Tableau 連接到 Azure 資料總管](tableau.md)