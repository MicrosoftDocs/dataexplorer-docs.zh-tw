---
title: 針對 Azure 資料總管叢集建立失敗進行疑難排解
description: 本文描述在 Azure 資料總管中建立叢集的疑難排解步驟。
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: 1bfec09d8ab953508ce89365f8431e7211dfd9cb
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874914"
---
# <a name="troubleshoot-failed-cluster-creation-of-azure-data-explorer"></a>疑難排解： Azure 資料總管的叢集建立失敗

萬一在 Azure 資料總管中建立叢集失敗，請遵循下列步驟。

1. 確定您有足夠的權限。 若要建立叢集，您必須是 Azure 訂用帳戶中「參與者」** 或「擁有者」** 角色的成員。 如有必要，請與訂用帳戶系統管理員合作，以便他們可以將您新增到適當的角色。

1. 確定沒有任何與叢集名稱 (您在 Azure 入口網站中的 [建立叢集]**** 底下輸入的名稱) 相關的驗證錯誤。

1. 查看 [Azure 服務健康情況儀表板](https://azure.microsoft.com/status/)。 請在您要嘗試建立叢集的區域中尋找 Azure 資料總管的狀態。

    如果狀態不是**良好** (綠色核取記號)，請在狀態改善之後嘗試建立叢集。

1. 若您仍然需要協助來解決問題，請在 [Azure 入口網站](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)中開啟支援要求。
