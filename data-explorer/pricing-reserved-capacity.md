---
title: 預付 Azure 資料資源管理器標記以節省資金
description: 瞭解如何購買 Azure 資料資源管理器保留容量以節省 Azure 資料資源管理器成本。
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: 6475a0190546ac577ae86ac1fde4adc41cd47595
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81501733"
---
# <a name="prepay-for-azure-data-explorer-markup-units-with-azure-data-explorer-reserved-capacity"></a>具有 Azure 資料資源管理員預留容量的 Azure 資料資源管理器標記單元的預付款

通過預付費標記單位與即用即付價格相比,使用 Azure 數據資源管理器節省資金。 使用 Azure 資料資源管理員保留容量時,您可以承諾使用 Azure 資料資源管理器一年或三年,以便對 Azure 資料資源管理器的標記成本進行大幅折扣。 要購買 Azure 資料資源管理器保留容量,只需指定術語,它將應用於所有區域中的 Azure 數據資源管理器的所有部署。

通過購買預訂,您可以預付一年或三年的加價費用。 購買預留後,Azure 數據資源管理器的標記費用將不再按即用即付費率收取。 Azure 資料資源管理器群集已在運行或新部署的群集將自動獲得好處。 此保留不包括與群集關聯的計算、網路或存儲費用。 這些資源的預留容量需要單獨購買。 在預留期限結束時,計費權益將過期,Azure 數據資源管理器標記單位以即用即付價格計費。 預訂不會自動續訂。 有關定價資訊,請參閱[Azure 資料資源管理員定價頁](https://azure.microsoft.com/pricing/details/data-explorer/)。

您可以在[Azure 門戶](https://portal.azure.com)中購買 Azure 資料資源管理器保留容量。 要購買 Azure 資料資源管理員保留容量:

* 您必須是至少一個企業或即用即付訂閱的擁有者。
* 針對企業訂用帳戶，必須在 [EA 入口網站](https://ea.azure.com)中啟用**新增保留執行個體**。 或者,如果禁用該設置,則必須是訂閱上的 EA 管理員。
* 對於雲端解決方案供應商 (CSP) 計畫,只有管理員代理或銷售代理才能購買 Azure 資料資源管理器預留容量。

有關企業客戶和即用即付客戶如何收取預訂費用的詳細資訊,請參閱:
* [了解 Enterprise 註冊的 Azure 保留使用量](/azure/cost-management-billing/reservations/understand-reserved-instance-usage-ea) 
* [瞭解即用即付訂閱的 Azure 預留使用方式](/azure/cost-management-billing/reservations/understand-reserved-instance-usage)。

## <a name="determine-the-right-markup-usage-before-purchase"></a>在購買前確定正確的加價用法

預留的大小應基於現有或即將部署的 Azure 資料資源管理器群集使用的 Azure 資料資源管理器標記單元的總數。 標記單元數等於生產中的 Azure 資料資源管理器引擎群集內核數(不包括開發/測試 SKU)。 

## <a name="buy-azure-data-explorer-reserved-capacity"></a>購買 Azure 資料資源管理員保留容量

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選擇**所有服務** > **預訂** > **立即購買**。 選擇 **"添加"**
1. 在 **「選擇產品類型」** 窗格中,選擇**Azure 資料資源管理器**以購買 Azure 資料資源管理器標記單元的新預留。 
1. 選擇 **"購買"**
1. 填寫必要欄位。 

    ![採購頁面](media/pricing-reserved-capacity/purchase-page.png)

1. 在 **「成本」** 部分中查看 Azure 資料資源管理器標記預留容量預留的成本。
1. 選取 [購買]  。
1. 選取 [檢視此保留]**** 以查看您的購買狀態。

## <a name="cancellations-and-exchanges"></a>取消和交換

如果需要取消 Azure 資料資源管理器預留容量預留,可能需要支付 12% 的提前終止費。 退款基於您的購買價格的最低價格或預訂的當前價格。 每年的退款金額上限為 50,000 美元。 您收到的退款是按比例計算的餘額扣除 12% 提前解約金的金額。 若要要求取消，請移至 Azure 入口網站中的保留，並選取 [退款]**** 以建立支援要求。

如果需要將 Azure 資料資源管理器預留容量預留更改為其他術語,則可以將其更改為其他值相等或更大的保留。 新保留的期限開始日期不會延續自交換的保留。 1 或 3 年的期限會從您建立新的保留時起算。 若要要求交換，請移至 Azure 入口網站中的保留，並選取 [交換]**** 以建立支援要求。

有關如何交換或退款預訂的更多資訊,請參閱[預訂交換和退款](/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations)。

## <a name="manage-your-reserved-capacity-reservation"></a>管理預留容量預留

Azure 資料資源管理器標記單位預留折扣將自動應用於與 Azure 資料資源管理器預留容量預留範圍和屬性匹配的標記單位數。 


> [!NOTE]
> * 您可以通過[Azure 門戶](https://portal.azure.com)、PowerShell、CLI 或透過 API 更新 Azure 資料資源管理器保留容量預留的範圍。
> * 要瞭解如何管理 Azure 資料資源管理員預留容量,請參考管理[Azure 資料資源管理員保留容量](/azure/cost-management-billing/reservations/understand-azure-data-explorer-reservation-charges)。

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure 保留項目，請參閱下列文章：

* [什麼是 Azure 保留項目？](/azure/cost-management-billing/reservations/save-compute-costs-reservations)
* [管理 Azure 保留項目](/azure/cost-management-billing/reservations/manage-reserved-vm-instance)
* [了解 Azure 保留折扣](/azure/cost-management-billing/reservations/understand-reservation-charges)
* [了解隨用隨付訂用帳戶的保留使用量](/azure/cost-management-billing/reservations/understand-reserved-instance-usage)
* [了解 Enterprise 註冊的保留項目使用量](/azure/cost-management-billing/reservations/understand-reserved-instance-usage-ea)
* [合作夥伴中心雲端解決方案提供者 (CSP) 計畫中的 Azure 保留項目](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡

如果您有問題或需要協助，請[建立支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。
