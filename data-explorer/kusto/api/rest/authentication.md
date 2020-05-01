---
title: 透過 HTTPS 進行驗證-Azure 資料總管 |Microsoft Docs
description: 本文說明如何透過 Azure 資料總管中的 HTTPS 進行驗證。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: e0910089d87d6bce6124cb7e4560c2fa7b92b847
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617981"
---
# <a name="authentication-over-https"></a>透過 HTTPS 進行驗證

使用 HTTPS 時，服務支援用來執行驗證`Authorization`的標準 HTTP 標頭。

支援的 HTTP 驗證方法如下：

* **Azure Active Directory**透過`bearer`方法 Azure Active Directory。

使用 Azure AD 進行驗證時， `Authorization`標頭的格式如下：

```txt
Authorization: bearer TOKEN
```

其中`TOKEN`是呼叫者藉由與 Azure AD 服務通訊來取得的存取權杖。 Token 具有下列屬性：

* 資源是服務 URI （例如`https://help.kusto.windows.net`）。
* Azure AD 服務端點為`https://login.microsoftonline.com/TENANT/`

其中`TENANT`是 Azure AD 的租使用者識別碼或名稱。 例如，在 Microsoft 租使用者底下建立的服務可以使用`https://login.microsoftonline.com/microsoft.com/`。 或者，僅針對使用者驗證，可以對提出要求`https://login.microsoftonline.com/common/`。

> [!NOTE]
> 當 Azure AD 服務端點在國家雲端中執行時，就會變更。
> 若要變更端點，請將環境變數`AadAuthorityUri`設定為所需的 URI。

如需詳細資訊，請參閱[驗證總覽](../../management/access-control/index.md)和[Azure AD 驗證的指南](../../management/access-control/how-to-authenticate-with-aad.md)。
