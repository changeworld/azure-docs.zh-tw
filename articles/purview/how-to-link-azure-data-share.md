---
title: 連接到 Azure Data Share
description: 本文說明如何將 Azure Data Share 帳戶與 Azure 範疇連線，以搜尋資產和追蹤資料歷程。
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: edea881d67d5a677339c6ff357c1ac45f5dfd420
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552348"
---
# <a name="how-to-connect-azure-data-share-and-azure-purview"></a>如何連接 Azure Data Share 和 Azure 範疇

本文討論如何將您的 [azure Data Share](../data-share/overview.md) 帳戶與 azure 範疇連線，並在您的資料資產中 (傳出和傳入的) 來管理共用資料集。 不同的資料治理角色可以探索和追蹤跨界限（例如組織、部門甚至資料中心）的資料歷程。

## <a name="common-scenarios"></a>常見案例

Data Share 歷程旨在提供根本原因分析和影響分析的詳細資訊。

### <a name="scenario-1-360-view-of-datasets-shared-inout-for-a-partner-organization-or-internal-department"></a>案例 1: 360 查看針對合作夥伴組織或內部部門共用的資料集

資料人員可以看到與夥伴組織雙向共用的所有資料集清單。 他們可以依組織名稱搜尋和探索資料集，並查看所有外寄和連入共用的完整觀點。

### <a name="scenario-2-root-cause-analysis---upstream-dependency-on-datasets-coming-into-organization-consumer-view-of-incoming-shares"></a>案例2：根本原因分析-傳入共用的資料集 (消費者視圖）上游相依性) 

因為來自外部 Data Share 帳戶的上游資料問題，所以報表的資訊不正確。 資料工程師可以瞭解上游失敗、告知原因，並進一步聯絡共用的擁有者，以修正造成資料差異的問題。

### <a name="scenario-3-impact-analysis-on-datasets-going-outside-organization-provider-view-of-outgoing-shares"></a>案例3：外送共用的資料集 (提供者觀點) 的影響分析

資料產生者想要知道在對資料集進行變更時，會受到影響的物件。 使用歷程，資料生產者可以輕鬆地瞭解使用 Azure Data Share 來取用資料的下游內部或外部夥伴的影響。

## <a name="azure-data-share-and-purview-connected-experience"></a>Azure Data Share 和範疇連線體驗

若要連接您的 Azure Data Share 和 Azure 範疇帳戶，請執行下列動作：

1. 建立範疇帳戶。 範疇帳戶將會收集所有 Data Share 歷程資訊。 您可以使用現有的帳戶，或建立新的範疇帳戶。

1. 將您的 Azure Data Share 連線至範疇帳戶。

    1. 在範疇入口網站中，您可以移至 [ **管理中心** ]，並將您的 Azure Data Share 連線到 [ **外部** 連線] 區段下。
    1. 在頂端列選取 [ **+ 新增** ]，在快顯提要欄位中尋找您的 Azure Data Share，然後新增 Data Share 帳戶。 將您的 Data Share 連接到範疇帳戶之後，執行快照集作業，以便在範疇中顯示 Data Share 資產和歷程資訊。

       :::image type="content" source="media/how-to-link-azure-data-share/connect-to-data-share.png" alt-text="用來連結 Azure Data Share 的管理中心":::

1. 在 Azure Data Share 中執行快照集。

    - 使用 Azure 範疇建立 Azure Data share 連線之後，您可以為現有的共用執行快照集。 
    - 如果您沒有任何現有的共用，請移至 Azure Data Share 入口網站，以[共用您的資料](../data-share/share-your-data.md)[並訂閱資料共用](../data-share/subscribe-to-data-share.md)。
    - 共用快照集完成後，您可以在範疇中查看相關聯的 Data Share 資產和歷程。

1. 探索 Data Share 帳戶，並在您的範疇帳戶中共用資訊。

    - 在範疇帳戶的 [首頁] 中，選取 **[依資產類型流覽]** ，然後選取 [ **Azure Data Share** ] 磚。 您可以搜尋帳戶名稱、共用名稱、共用快照集或夥伴組織。 否則，請在 [搜尋結果] 頁面上，針對帳戶名稱、共用類型 (傳送與接收的共用) 的共用類型套用篩選。

       :::image type="content" source="media/how-to-link-azure-data-share/azure-data-share-search-result-page.png" alt-text="搜尋結果頁面中的 Azure Data share":::

    >[!Important]
    >針對要在範疇中顯示的 Data Share 資產，在您將 Data Share 連線到範疇之後，就必須執行快照集作業。

1. 追蹤與 Azure Data Share 共用的資料集歷程記錄。

    - 從 [範疇搜尋 **結果] 頁面** 中，選擇 [接收/傳送)  (資料共用快照集，然後選取 [歷程] 索引標籤，以查看具有上游和下游相依性的歷程圖表。

    :::image type="content" source="media/how-to-link-azure-data-share/azure-data-share-lineage.png" alt-text="使用 Azure Data Share 共用的資料集歷程":::

## <a name="next-steps"></a>後續步驟

- [目錄歷程使用者指南](catalog-lineage-user-guide.md)
- [歷程的 Azure Data Factory 連結](how-to-link-azure-data-factory.md)
