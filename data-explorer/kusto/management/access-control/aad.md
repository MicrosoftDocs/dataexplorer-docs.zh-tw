---
title: Kusto Azure Active Directory (AAD) 驗證-Azure 資料總管
description: 本文說明 Azure 資料總管中 (AAD) 驗證 Azure Active Directory。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 09/13/2019
ms.openlocfilehash: a511f96df09fa9ce4089a0af77ebbe09269c9b44
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92343431"
---
# <a name="azure-active-directory-aad-authentication"></a>Azure Active Directory (AAD) 驗證

Azure Active Directory (AAD) 是 Azure 慣用的多租用戶雲端目錄服務，能夠驗證安全性主體或與其他識別提供者 (例如 Microsoft 的 Active Directory) 同盟。

AAD 允許應用各種不同類型的 (web 應用程式、Windows 桌面應用程式、通用應用程式、行動應用程式等 ) ，以一致的驗證和使用 Kusto 服務。

AAD 支援許多驗證案例。
如果驗證期間存在使用者，則應該由 AAD 使用者驗證將使用者驗證至 AAD。
在某些情況下，即使沒有任何使用者以互動方式出現，也需要服務使用 Kusto。 在這種情況下，您應該使用應用程式密碼來驗證應用程式，如 AAD 應用程式驗證中所述。

一般 Kusto 支援下列驗證方法，包括透過其 .NET 程式庫：

* 互動式使用者驗證-此模式需要互動式功能，如有需要，登入 UI 將會出現
* 使用先前針對 Kusto 發出的現有 AAD 權杖進行使用者驗證
* 使用 AppID 和共用密碼進行應用程式驗證
* 使用本機安裝的509v2 憑證或以內嵌方式提供的憑證進行應用程式驗證
* 使用先前針對 Kusto 發出的現有 AAD 權杖進行應用程式驗證
* 使用對另一個資源發出之 AAD 權杖的使用者或應用程式驗證，提供該資源與 Kusto 之間存在的信任

如需指引和範例，請參閱 [Kusto 連接字串](../../api/connection-strings/kusto.md) 參考。

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

## <a name="aad-server-application-permissions"></a>AAD 伺服器應用程式許可權

在一般情況下，AAD 伺服器應用程式可以定義多個許可權 (例如，唯讀許可權和讀取寫入器許可權) ，而且 AAD 用戶端應用程式可能會在要求授權權杖時決定所需的許可權。 在取得權杖的過程中，系統會要求使用者授權 AAD 用戶端應用程式，以代表使用者採取行動，以擁有這些許可權。 如果使用者核准，這些許可權將會列在對 AAD 用戶端應用程式發出之權杖的範圍宣告中。



AAD 用戶端應用程式設定為要求使用者 (「存取權 Kusto」許可權，而 AAD 會呼叫「資源擁有者」 ) 。

## <a name="kusto-client-sdk-as-an-aad-client-application"></a>Kusto 用戶端 SDK 作為 AAD 用戶端應用程式

當 Kusto 用戶端程式庫叫用 ADAL (AAD 用戶端程式庫) 以取得與 Kusto 通訊的權杖時，它會提供下列資訊：

1. 從呼叫端接收的 AAD 租使用者
2. AAD 用戶端應用程式識別碼
3. AAD 用戶端資源識別碼
4. AAD ReplyUrl (當驗證成功完成後，AAD 服務將重新導向的 URL;ADAL 接著會捕捉此重新導向，並將其) 的授權碼解壓縮。
5. 叢集 URI ( ' https://Cluster-and-region.kusto.windows.net ' ) 。

ADAL 傳回給 Kusto 用戶端程式庫的權杖會以 Kusto AAD 伺服器應用程式作為物件，並以「存取 Kusto」許可權作為領域。

## <a name="authenticating-with-aad-programmatically"></a>以程式設計方式向 AAD 進行驗證

下列文章說明如何以程式設計方式向 AAD 驗證 Kusto：

* [如何布建 AAD 應用程式](../../../provision-azure-ad-app.md)
* [如何執行 AAD 驗證](./how-to-authenticate-with-aad.md)