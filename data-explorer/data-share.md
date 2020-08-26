---
title: 使用 Azure Data Share 與 Azure 資料總管共用資料
description: 瞭解如何與 Azure 資料總管和 Azure Data Share 共用您的資料。
author: orspod
ms.author: orspodek
ms.reviewer: maraheja
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/14/2020
ms.openlocfilehash: 01c8edee98a8f44ae4ad480cb14a327c4edcee83
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88873180"
---
# <a name="use-azure-data-share-to-share-data-with-azure-data-explorer"></a>使用 Azure Data Share 與 Azure 資料總管共用資料

有許多傳統的方式可以共用資料，例如透過檔案共用、FTP、電子郵件和 Api。 這些方法要求雙方都必須建立和維護資料管線，以在小組和組織之間移動資料。 有了 Azure 資料總管，您就可以輕鬆且安全地與公司或外部合作夥伴的人員共用您的資料。 共用會以近乎即時的方式進行，而不需要建立或維護資料管線。 提供者端上的所有資料庫變更（包括架構和資料）立即可在取用者端使用。

[![Azure 星期五影片](https://img.youtube.com/vi/Q3MJv90PegE/0.jpg)](https://www.youtube.com/watch?v=Q3MJv90PegE?&autoplay=1)

Azure 資料總管會將儲存體和計算分離，讓客戶能夠在相同的基礎儲存體上執行多個計算 (唯讀) 實例。 您可以將資料庫附加為資料庫 [，這](follower.md)是遠端叢集上的唯讀資料庫。

## <a name="configure-data-sharing"></a>設定資料共用 

您可以使用下列其中一種方法來設定資料共用：

* 使用 [Azure Data Share](/azure/data-share/) 來傳送和管理整個公司或外部夥伴和客戶的邀請和共用。 Azure Data Share 會使用取用者 [資料庫](follower.md) 來建立提供者與取用者的 Azure 資料總管叢集之間的符號連結。 此選項提供單一窗格，可讓您在 Azure 資料總管叢集和其他資料服務之間查看及管理所有資料共用。 Azure Data Share 也可讓您在不同 Azure Active 直接租使用者的組織之間共用資料。
* 只有在您需要額外計算來向外延展以因應報告需求，且您是這兩個叢集的系統管理員時，才會直接設定「僅限」 [資料庫](follower.md) 。

> [!Note] 
> 建立共用關聯性時，Azure Data Share 會在提供者與取用者的 Azure 資料總管叢集之間建立符號連結。 如果資料提供者撤銷存取權，則會刪除符號連結，而且資料取用者無法再使用共用資料庫 (s) 。

:::image type="content" source="media/data-share/adx-datashare-image.png" alt-text="Azure 資料總管資料共用":::

資料提供者可以在資料庫層級或叢集層級共用資料。 共用資料庫的叢集是領導者叢集，而接收共用的叢集是一種可供進行的叢集。 一或多個主資料庫叢集可以遵循一或多個領導者叢集資料庫。 進行中的叢集會定期進行同步處理，以檢查是否有變更。 根據中繼資料和資料的整體大小而定，領導者和資料的延遲時間會從幾秒鐘到幾分鐘。 資料會快取在取用者叢集上，且僅供讀取或查詢作業使用，但有例外狀況可覆寫熱快取原則和資料庫許可權。 在執行程式叢集上執行的查詢會使用本機快取，而不會使用領導者叢集的資源。

## <a name="prerequisites"></a>必要條件

1. 如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。
1. 建立適用于領導人和[程式的提供者叢集和資料庫](create-cluster-database-portal.md)。
1. 使用內嵌[總覽](ingest-data-overview.md)中所討論的其中一種方法，將[資料](ingest-sample-data.md)內嵌至領導者資料庫。

## <a name="data-provider---share-data"></a>資料提供者-共用資料

遵循影片中的指示來建立資料共用帳戶、新增資料集，以及傳送邀請。

[![資料提供者-共用資料](https://img.youtube.com/vi/QmsTnr90_5o/0.jpg)](https://youtu.be/QmsTnr90_5o?&autoplay=1)

## <a name="data-consumer---receive-data"></a>資料取用者-接收資料

遵循影片中的指示來接受邀請、建立資料共用帳戶，以及對應至取用者叢集。

[![資料取用者-接收資料](https://img.youtube.com/vi/vBq6iFaCpdA/0.jpg)](https://youtu.be/vBq6iFaCpdA?&autoplay=1)

資料取用者現在可以移至其 Azure 資料總管叢集，以將共用資料庫的許可權授與使用者，並存取資料。 使用批次模式內嵌至來源 Azure 資料總管叢集中的資料，將會在幾秒鐘內顯示于目標叢集上幾分鐘。

## <a name="limitations"></a>限制

* [的資料庫限制](follower.md#limitations)

## <a name="next-steps"></a>後續步驟

* [Azure Data Share 文件](/azure/data-share/)
* 如需有關「取得」叢集的詳細資訊， [請參閱「](follower.md)
