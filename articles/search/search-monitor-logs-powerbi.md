---
title: 使用 Power BI 將 Azure 認知搜尋記錄和計量視覺化
description: 使用 Power BI 將 Azure 認知搜尋記錄和計量視覺化
manager: eladz
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 90691fda7b0bf58768c7e9be6a78fb27a7807186
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91400361"
---
# <a name="visualize-azure-cognitive-search-logs-and-metrics-with-power-bi"></a>使用 Power BI 將 Azure 認知搜尋記錄和計量視覺化
[Azure 認知搜尋](./search-what-is-azure-search.md) 可讓您將搜尋服務的作業記錄和服務計量儲存在 Azure 儲存體帳戶中。 此頁面提供如何透過 Power BI 範本應用程式來將該資訊視覺化的指示。 應用程式會提供搜尋服務的詳細資訊，包括搜尋、索引、作業和服務計量的相關資訊。

您可以在[Power BI Apps marketplace](https://appsource.microsoft.com/marketplace/apps)中找到 Power BI 範本應用程式**Azure 認知搜尋：分析記錄和計量**。

## <a name="how-to-get-started-with-the-app"></a>如何開始使用應用程式

1. 啟用搜尋服務的計量和資源記錄：

    1. 建立或識別現有的 [Azure 儲存體帳戶](../storage/common/storage-account-create.md) ，您可以在其中保存記錄檔
    1. 流覽至您在 Azure 入口網站中的 Azure 認知搜尋服務
    1. 在左側資料行的 [監視] 區段下，選取 [**診斷設定**]。

        :::image type="content" source="media/search-monitor-logs-powerbi/diagnostic-settings.png" alt-text="顯示如何在 Azure 認知搜尋服務的 [監視] 區段中選取診斷設定的螢幕擷取畫面。" border="false":::

    1. 選取 [ **+ 新增診斷設定**]
    1. 查看封存 **至儲存體帳戶**、提供您的儲存體帳戶資訊，以及檢查 **insight-logs-operationlogs** 和 **AllMetrics**

        :::image type="content" source="media/search-monitor-logs-powerbi/add-diagnostic-setting.png" alt-text="顯示如何在 Azure 認知搜尋服務的 [監視] 區段中選取診斷設定的螢幕擷取畫面。":::
    1. 選取 [儲存]。

1. 啟用記錄之後，請使用您的搜尋服務來開始產生記錄和計量。 最多需要一個小時的時間，容器才會出現在含有這些記錄的 Blob 儲存體中。 您會看到搜尋流量記錄的 **insight-logs-operationlogs** 容器，以及適用于度量的 **見解-計量 pt1m** 容器。

1. 在 [Power BI Apps marketplace](https://appsource.microsoft.com/marketplace/apps) 中尋找 Azure 認知搜尋 Power BI 應用程式，並將它安裝到新的工作區或現有的工作區。 此應用程式稱為 **Azure 認知搜尋：分析記錄和計量**。

1. 安裝應用程式之後，請從 Power BI 的應用程式清單中選取應用程式。

    :::image type="content" source="media/search-monitor-logs-powerbi/azure-search-app-tile.png" alt-text="顯示如何在 Azure 認知搜尋服務的 [監視] 區段中選取診斷設定的螢幕擷取畫面。":::

1. 選取 **[** 連線] 以連接您的資料

    :::image type="content" source="media/search-monitor-logs-powerbi/get-started-with-your-new-app.png" alt-text="顯示如何在 Azure 認知搜尋服務的 [監視] 區段中選取診斷設定的螢幕擷取畫面。":::

1. 輸入包含記錄和計量的儲存體帳戶名稱。 根據預設，應用程式會查看過去10天的資料，但可使用 **days** 參數變更此值。

    :::image type="content" source="media/search-monitor-logs-powerbi/connect-to-storage-account.png" alt-text="顯示如何在 Azure 認知搜尋服務的 [監視] 區段中選取診斷設定的螢幕擷取畫面。":::

1. 選取 [ **金鑰** ] 作為驗證方法，並提供您的儲存體帳戶金鑰。 選取 [ **私** 用] 作為 [隱私權等級]。 按一下 [登入]  開始載入程序。

    :::image type="content" source="media/search-monitor-logs-powerbi/connect-to-storage-account-step-two.png" alt-text="顯示如何在 Azure 認知搜尋服務的 [監視] 區段中選取診斷設定的螢幕擷取畫面。":::

1. 等候資料重新整理。 這可能需要一些時間，視您擁有的資料量而定。 您可以根據下列指標，查看資料是否仍在重新整理中。

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-refreshing.png" alt-text="顯示如何在 Azure 認知搜尋服務的 [監視] 區段中選取診斷設定的螢幕擷取畫面。":::

1. 當資料重新整理完成之後，請選取 **Azure 認知搜尋報表** 來查看報表。

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-select-report.png" alt-text="顯示如何在 Azure 認知搜尋服務的 [監視] 區段中選取診斷設定的螢幕擷取畫面。":::![]()

1. 開啟報表之後，請務必重新整理頁面，以便與您的資料一起開啟。

    :::image type="content" source="media/search-monitor-logs-powerbi/powerbi-search.png" alt-text="顯示如何在 Azure 認知搜尋服務的 [監視] 區段中選取診斷設定的螢幕擷取畫面。":::

## <a name="how-to-change-the-app-parameters"></a>如何變更應用程式參數
如果您想要將不同儲存體帳戶的資料視覺化，或變更要查詢的資料天數，請遵循下列步驟來變更 **days** 和 **StorageAccount** 參數。

1. 流覽至您的 Power BI 應用程式，尋找您的 Azure 認知搜尋應用程式，然後選取 [ **編輯應用程式** ] 按鈕以查看工作區。

    :::image type="content" source="media/search-monitor-logs-powerbi/azure-search-app-tile-edit.png" alt-text="顯示如何在 Azure 認知搜尋服務的 [監視] 區段中選取診斷設定的螢幕擷取畫面。":::

1. 從資料集選項中選取 [ **設定** ]。

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-select-settings.png" alt-text="顯示如何在 Azure 認知搜尋服務的 [監視] 區段中選取診斷設定的螢幕擷取畫面。":::

1. 在 [資料集] 索引標籤中，變更參數值 **並選取 [** 套用]。 如果連接發生問題，請更新相同頁面上的資料來源認證。

1. 流覽回工作區，然後從資料集選項中選取 [ **立即** 重新整理]。

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-select-refresh-now.png" alt-text="顯示如何在 Azure 認知搜尋服務的 [監視] 區段中選取診斷設定的螢幕擷取畫面。":::

1. 開啟報表以查看更新的資料。 您可能也需要重新整理報表，以查看最新的資料。

## <a name="troubleshooting"></a>疑難排解
如果您發現您看不到您的資料，請遵循下列疑難排解步驟：

1. 開啟報表並重新整理頁面，以確定您正在查看最新的資料。 報表中有一個選項可重新整理資料。 選取此，以取得最新的資料。

1. 請確認您提供的儲存體帳戶名稱和存取金鑰正確無誤。 儲存體帳戶名稱應對應至使用搜尋服務記錄所設定的帳戶。

1. 確認您的儲存體帳戶包含容器 **見解-記錄-insight-logs-operationlogs** 和 **見解-計量 pt1m** ，且每個容器都有資料。 記錄和計量會在幾個資料夾中。

1. 請檢查資料集是否仍在重新整理。 重新整理狀態指標會顯示于上述步驟8中。 如果仍在重新整理中，請等候重新整理完成，以開啟並重新整理報表。

## <a name="next-steps"></a>後續步驟
[深入瞭解 Azure 認知搜尋](./index.yml)

[Power BI 是什麼？](/power-bi/fundamentals/power-bi-overview)

[Power BI 服務中的設計工具基本概念](/power-bi/service-basic-concepts)