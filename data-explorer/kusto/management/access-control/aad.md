---
title: Azure 活動目錄 (AAD) 身份驗證 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 Azure 活動目錄 (AAD) 身份驗證。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/13/2019
ms.openlocfilehash: 637252b91af53198b3ee494309857b2d4b6e6828
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522928"
---
# <a name="azure-active-directory-aad-authentication"></a>Azure 動作目錄 (AAD) 認證

Azure Active Directory (AAD) 是 Azure 慣用的多租用戶雲端目錄服務，能夠驗證安全性主體或與其他識別提供者 (例如 Microsoft 的 Active Directory) 同盟。

AAD 允許各種應用程式(Web 應用程式、Windows 桌面應用程式、通用應用程式、行動應用程式等)統一驗證和使用 Kusto 服務。

AAD 支援多種身份驗證方案。
如果在身份驗證期間存在使用者,則應通過 AAD 使用者身份驗證向 AAD 使用者身份驗證對使用者進行身份驗證。
在某些情況下,即使沒有使用者以交互方式存在,也希望服務使用 Kusto。 在這種情況下,應使用應用程式密鑰對應用程式進行身份驗證,如 AAD 應用程式身份驗證中所述。

Kusto 通常支援以下身份驗證方法,包括透過其 .NET 函式庫:

* 互動式使用者身份驗證 ─ 此模式需要互動性,如果需要,登入 UI 將彈出
* 以前為 Kusto 發行的現有 AAD 權杖的使用者身份驗證
* 應用程式認證與 AppID 和分享金鑰
* 使用本地安裝的 X.509v2 憑證或內聯憑證進行應用程式身份驗證
* 這個使用者為 Kusto 發行的現有 AAD 權杖的應用程式身份驗證
* 使用為另一資源頒發的 AAD 權杖的使用者或應用程式身份驗證,前提是該資源與 Kusto 之間存在信任

有關指南和示例,請參閱[Kusto 連接字串](../../api/connection-strings/kusto.md)參考。

## <a name="user-authentication"></a>使用者驗證

使用者驗證會在使用者向 AAD 出示認證 (或向某些與 AAD 同盟的識別提供者，例如 ADFS) 時進行，並取回可向 Kusto 服務出示的安全性權杖。 Kusto 服務並不在意安全性權杖的取得方式，它會在意權杖是否有效，以及 AAD (或同盟 IdP) 所放入的資訊。

在用戶端上，Kusto 支援互動式驗證，其中 AAD 用戶端程式庫 ADAL 或類似的程式碼會要求使用者輸入認證。 它也支援以權杖為基礎的驗證，其中使用 Kusto 的應用程式會取得有效的使用者權杖並予以出示。 最後，它可支援使用 Kusto 的應用程式為其他一些服務 (而非 Kusto) 取得有效的使用者權杖，前提是該資源與 Kusto 之間有信任關係。

如需有關如何使用 Kusto 用戶端程式庫，以及使用 AAD 向 Kusto 進行驗證的詳細資訊，請參閱 [Kusto 連接字串](../../api/connection-strings/kusto.md)。

## <a name="application-authentication"></a>應用程式驗證

若要求並未與特定使用者相關聯，或沒有可供輸入認證的使用者，則可能使用 AAD 應用程式驗證流程。 在此流程中，應用程式會藉由出示一些秘密資訊來向 AAD (或同盟 IdP) 進行驗證。 各種 Kusto 用戶端都支援下列案例：

* 使用本機安裝的 X.509v2 憑證進行應用程式驗證。
* 使用提供給用戶端程式庫的 X.509v2 憑證作為位元組資料流，進行應用程式驗證。
* 使用 AAD 應用程式識別碼和 AAD 應用程式金鑰 (相當於應用程式的使用者名稱/密碼驗證) 進行應用程式驗證。
* 使用先前取得的有效 AAD 權杖 (發行至 Kusto) 進行應用程式驗證。
* 使用先前取得並發行給其他資源的有效 AAD 權杖進行應用程式驗證，前提是該資源與 Kusto 之間有信任關係。

## <a name="aad-server-application-permissions"></a>AAD 伺服器應用程式權限

在一般情況下,AAD 伺服器應用程式可以定義多個許可權(例如,唯讀權限和讀取寫入器許可權),AAD 用戶端應用程式可以決定請求授權權杖時需要哪些許可權。 作為權杖獲取的一部分,將要求使用者授權 AAD 用戶端應用程式代表使用者執行具有具有這些許可權的授權。 如果使用者批准,這些許可權將列在頒發給 AAD 用戶端應用程式的權杖的範圍聲明中。



AAD 用戶端應用程式配置為向使用者請求"訪問庫斯托"許可權(AAD 稱之為"資源擁有者")。

## <a name="kusto-client-sdk-as-an-aad-client-application"></a>Kusto 用戶端 SDK 作為 AAD 用戶端應用程式

當 Kusto 用戶端程式庫叫用 ADAL (AAD 用戶端程式庫) 以取得與 Kusto 通訊的權杖時，它會提供下列資訊：

1. 從呼叫者接收的 AAD 租戶
2. AAD 用戶端應用程式識別碼
3. AAD 客戶端資源識別碼
4. AAD 回覆Url(身份驗證成功完成後 AAD 服務將重定向到的 URL;然後,ADAL 捕獲此重定向並從中提取授權代碼)。
5. 群集 URI ('')。https://Cluster-and-region.kusto.windows.net

ADAL 傳回到 Kusto 用戶端庫的權杖具有庫斯托 AAD 伺服器應用程式作為存取群體,以及作為作用網域的「訪問庫斯托」許可權。

## <a name="authenticating-with-aad-programmatically"></a>以程式設計使用 AAD 進行認證

以下文章說明如何使用 AAD 以程式設計方式向 Kusto 進行身份驗證:

* [如何預先定義 AAD 應用程式](./how-to-provision-aad-app.md)
* [如何執行 AAD 認證](./how-to-authenticate-with-aad.md)

