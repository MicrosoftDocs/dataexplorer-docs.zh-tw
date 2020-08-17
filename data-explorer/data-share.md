---
title: 使用 Azure 資料共用與 Azure 資料總管共用資料
description: 深入瞭解如何使用 Azure 資料總管和 Azure 資料共用來共用您的資料。
author: orspod
ms.author: orspodek
ms.reviewer: maraheja
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/14/2020
ms.openlocfilehash: 29d3c10dc08d0506f43af7127cbf705a8b1881c1
ms.sourcegitcommit: ec191391f5ea6df8c591e6d747c67b2c46f98ac4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2020
ms.locfileid: "88259919"
---
# <a name="use-azure-data-share-to-share-data-with-azure-data-explorer"></a>使用 Azure 資料共用與 Azure 資料總管共用資料

有許多傳統的方式可共用資料，例如透過檔案共用、FTP、電子郵件和 Api。 這些方法需要雙方建立和維護資料管線，以便在小組與組織之間移動資料。 有了 Azure 資料總管，您就可以輕鬆且安全地與公司或外部合作夥伴的人員共用您的資料。 共用會以近乎即時的方式進行，而不需要建立或維護資料管線。 提供者端上的所有資料庫變更（包括架構和資料）都會立即在取用者端提供。

[![Azure 星期五影片](https://img.youtube.com/vi/Q3MJv90PegE/0.jpg)](https://www.youtube.com/watch?v=Q3MJv90PegE?&autoplay=1)

Azure 資料總管會將儲存體和計算分離，讓客戶能夠在相同的基礎儲存體上執行多個計算 (唯讀) 實例。 您可以附加資料庫做為遠端叢集上的唯讀資料庫，做為使用者的 [資料庫](follower.md)。

## <a name="configure-data-sharing"></a>設定資料共用 

您可以使用下列其中一種方法來設定資料共用：

* 使用 [Azure 資料共用](/azure/data-share/) 來傳送和管理整個公司或外部合作夥伴和客戶的邀請和共用。 Azure 資料共用會使用一個後向 [資料庫](follower.md) ，在提供者和取用者的 Azure 資料總管叢集之間建立符號連結。 此選項提供單一窗格，讓您能夠跨 Azure 資料總管叢集和其他資料服務來查看及管理所有資料共用。 Azure 資料共用也可讓您跨不同 Azure Active 直接租使用者中的組織共用資料。
* 只有當您需要額外的計算來向外延展以滿足報告需求，而且您是這兩個叢集的系統管理員時，才會直接設定「資料移」 [資料庫](follower.md) 。

> [!Note] 
> 建立共用關聯性後，Azure 資料共用會在提供者與取用者的 Azure 資料總管叢集之間建立符號連結。 如果資料提供者撤銷存取權，則會刪除符號連結，而且共用資料庫 (s) 不再提供給資料取用者使用。

:::image type="content" source="media/data-share/adx-datashare-image.png" alt-text="Azure 資料總管資料共用":::

資料提供者可以在資料庫層級或叢集層級共用資料。 共用資料庫的叢集是領導者叢集，而接收共用的叢集是「進行中」叢集。 一或多個領導者叢集資料庫可供一個進行中的叢集使用。 「進行中」叢集會定期進行同步處理，以檢查是否有變更。 根據中繼資料和資料的整體大小而定，領導者和消費者之間的延遲時間會從幾秒鐘到幾分鐘之間而有所不同。 資料會在取用者叢集上快取，而且僅供讀取或查詢作業使用，但覆寫熱快取原則和資料庫許可權除外。 在進行中的叢集上執行的查詢會使用本機快取，而不會使用領導者叢集的資源。

## <a name="prerequisites"></a>必要條件

1. 如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。
1. 為領導者和進行中[建立提供者叢集和資料庫](create-cluster-database-portal.md)。
1. 使用內嵌[總覽](ingest-data-overview.md)中所討論的各種方法之一，將[資料](ingest-sample-data.md)內嵌至領導者資料庫。

## <a name="data-provider---share-data"></a>資料提供者-共用資料

遵循影片中的指示來建立資料共用帳戶、新增資料集，以及傳送邀請。

[![資料提供者-共用資料](https://img.youtube.com/vi/QmsTnr90_5o/0.jpg)](https://youtu.be/QmsTnr90_5o?&autoplay=1)

## <a name="data-consumer---receive-data"></a>資料取用者-接收資料

依照影片中的指示接受邀請、建立資料共用帳戶，然後對應至取用者叢集。

[![資料取用者-接收資料](https://img.youtube.com/vi/vBq6iFaCpdA/0.jpg)](https://youtu.be/vBq6iFaCpdA?&autoplay=1)

資料取用者現在可以移至其 Azure 資料總管叢集，以授與使用者共用資料庫和存取資料的許可權。 使用批次模式內嵌至來源 Azure 資料總管叢集的資料會在幾秒內顯示于目標叢集上。

## <a name="limitations"></a>限制

* [後向資料庫限制](follower.md#limitations)

## <a name="next-steps"></a>後續步驟

* [Azure Data Share 文件](/azure/data-share/)
* 如需有關「後向叢集」的詳細資訊， [請參閱](follower.md)
