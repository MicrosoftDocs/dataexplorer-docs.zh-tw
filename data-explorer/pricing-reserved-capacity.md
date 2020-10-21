---
title: 預付 Azure 資料總管標記以節省成本
description: 瞭解如何購買 Azure 資料總管保留容量，以節省您的 Azure 資料總管成本。
author: orspod
ms.author: orspodek
ms.reviewer: avnera
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: aefbde03b123e9c5413b1ed39e85db096f3ff3d7
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92343448"
---
# <a name="prepay-for-azure-data-explorer-markup-units-with-azure-data-explorer-reserved-capacity"></a>使用 Azure 資料總管保留容量預付 Azure 資料總管加值單位

相較于隨用隨付價格，Azure 資料總管 by 預付 for 標記單位來節省成本。 您可以使用 Azure 資料總管保留容量，在一或三年期內承諾使用 Azure 資料總管，以獲得 Azure 資料總管加值成本的大量折扣。 若要購買 Azure 資料總管保留容量，您只需要指定詞彙，它會套用至所有區域中的所有 Azure 資料總管部署。

購買保留之後，您就可以預先支付一年或三年期的加值成本。 當您購買保留專案時，與保留屬性相符的 Azure 資料總管加值費用就不再以隨用隨付費率計費。 已在執行或剛部署的 Azure 資料總管叢集將會自動獲得權益。 此保留未涵蓋與叢集相關聯的計算、網路或儲存體費用。 需要另外購買這些資源的保留容量。 在保留期限結束時，帳單權益會過期，而 Azure 資料總管加值單位會以隨用隨付價格計費。 保留不會自動續約。 如需定價資訊，請參閱 [Azure 資料總管定價頁面](https://azure.microsoft.com/pricing/details/data-explorer/)。

您可以在 [Azure 入口網站](https://portal.azure.com)購買 Azure 資料總管保留容量。 若要購買 Azure 資料總管保留容量：

* 您必須是至少一個企業或隨用隨付訂用帳戶的擁有者。
* 針對企業訂用帳戶，必須在 [EA 入口網站](https://ea.azure.com)中啟用**新增保留執行個體**。 或者，如果該設定已停用，您必須是訂用帳戶上的 EA 系統管理員。
* 針對雲端解決方案提供者 (CSP) 方案，只有系統管理員代理程式或銷售專員可以購買 Azure 資料總管保留容量。

如需企業客戶和隨用隨付客戶如何針對保留購買收費的詳細資訊，請參閱：
* [了解 Enterprise 註冊的 Azure 保留使用量](/azure/cost-management-billing/reservations/understand-reserved-instance-usage-ea) 
* [瞭解隨用隨付訂用帳戶的 Azure 保留使用量](/azure/cost-management-billing/reservations/understand-reserved-instance-usage)。

## <a name="determine-the-right-markup-usage-before-purchase"></a>在購買前判斷正確的標記使用方式

保留大小應該以現有或即將部署的 Azure 資料總管叢集所使用的 Azure 資料總管標記單位總數為基礎。 標記單位數目等於生產環境中的 Azure 資料總管引擎叢集核心數目 (不包含開發/測試 SKU) 。 

## <a name="buy-azure-data-explorer-reserved-capacity"></a>購買 Azure 資料總管保留容量

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取**All services**[  >  **Reservations**  >  **立即購買**所有服務保留]。 選取 [新增]
1. 在 [ **選取產品類型** ] 窗格中，選取 [ **Azure 資料總管** 為 azure 資料總管標記單位購買新的保留。 
1. 選取 **購買**
1. 填寫必要欄位。 

    ![購買頁面](media/pricing-reserved-capacity/purchase-page.png)

1. 在 [ **成本** ] 區段中，檢查 Azure 資料總管標記保留容量保留的成本。
1. 選取 [購買]。
1. 選取 [檢視此保留]**** 以查看您的購買狀態。

## <a name="cancellations-and-exchanges"></a>取消和交換

如果您需要取消 Azure 資料總管保留容量保留，可能會有12% 的提前終止費用。 退款是根據您的購買價格或目前保留價格的最低價格。 每年的退款金額上限為 50,000 美元。 您收到的退款是按比例計算的餘額扣除 12% 提前解約金的金額。 若要要求取消，請移至 Azure 入口網站中的保留，並選取 [退款]**** 以建立支援要求。

如果您需要將 Azure 資料總管保留容量保留變更為另一個期限，您可以將它與相同或更高價值的另一個保留交換。 新保留的期限開始日期不會延續自交換的保留。 1 或 3 年的期限會從您建立新的保留時起算。 若要要求交換，請移至 Azure 入口網站中的保留，並選取 [交換]**** 以建立支援要求。

如需如何交換或退款保留的詳細資訊，請參閱 [保留交換和退款](/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations)。

## <a name="manage-your-reserved-capacity-reservation"></a>管理保留容量保留

Azure 資料總管加值單位保留折扣會自動套用至符合 Azure 資料總管保留容量保留範圍和屬性的標記單位數目。 


> [!NOTE]
> * 您可以透過 [Azure 入口網站](https://portal.azure.com)、POWERSHELL、CLI 或 API 來更新 Azure 資料總管保留容量保留的範圍。
> * 若要瞭解如何管理 Azure 資料總管保留容量保留，請參閱 [管理 azure 資料總管保留容量](/azure/cost-management-billing/reservations/understand-azure-data-explorer-reservation-charges)。

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure 保留項目，請參閱下列文章：

* [什麼是 Azure 保留項目？](/azure/cost-management-billing/reservations/save-compute-costs-reservations)
* [管理 Azure 保留項目](/azure/cost-management-billing/reservations/manage-reserved-vm-instance)
* [了解 Azure 保留折扣](/azure/cost-management-billing/reservations/understand-reservation-charges)
* [了解隨用隨付訂用帳戶的保留使用量](/azure/cost-management-billing/reservations/understand-reserved-instance-usage)
* [了解 Enterprise 註冊的保留項目使用量](/azure/cost-management-billing/reservations/understand-reserved-instance-usage-ea)
* [合作夥伴中心雲端解決方案提供者 (CSP) 計畫中的 Azure 保留項目](/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡

如果您有問題或需要協助，請[建立支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。