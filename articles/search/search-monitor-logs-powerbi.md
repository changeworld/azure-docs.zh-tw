---
title: 使用 Power BI 將 Azure 認知搜尋記錄和計量視覺化
description: 使用 Power BI 將 Azure 認知搜尋記錄和計量視覺化
manager: eladz
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: e01ac332e61f51909ff1617f1716cd719b67c319
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82127876"
---
# <a name="visualize-azure-cognitive-search-logs-and-metrics-with-power-bi"></a>使用 Power BI 將 Azure 認知搜尋記錄和計量視覺化
[Azure 認知搜尋](https://docs.microsoft.com/azure/search/search-what-is-azure-search)可讓您將有關搜尋服務的作業記錄和服務計量儲存在 Azure 儲存體帳戶中。 本頁面提供如何透過 Power BI 範本應用程式來視覺化該資訊的指示。 應用程式會提供搜尋服務的詳細見解，包括搜尋、索引、作業和服務計量的相關資訊。

您可以在[Power BI Apps marketplace](https://appsource.microsoft.com/marketplace/apps)中找到 Power BI 範本應用程式**Azure 認知搜尋：分析記錄和計量**。

## <a name="how-to-get-started-with-the-app"></a>如何開始使用應用程式

1. 啟用搜尋服務的計量和資源記錄功能：

    1. 建立或識別您可以封存記錄的現有[Azure 儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
    1. 在 Azure 入口網站中流覽至您的 Azure 認知搜尋服務
    1. 在左側資料行的 [監視] 區段下，選取 [**診斷設定**]。

        ![](media/search-monitor-logs-powerbi/diagnostic-settings.png)

    1. 選取 [ **+ 新增診斷設定**]
    1. 勾選 [封存**至儲存體帳戶**]，提供您的儲存體帳戶資訊，並檢查**insights-logs-operationlogs**和**AllMetrics**

        ![](media/search-monitor-logs-powerbi/add-diagnostic-setting.png)
    1. 選取 [儲存]  。

1. 啟用記錄之後，請使用您的搜尋服務開始產生記錄和計量。 最多需要一小時的時間，容器才會出現在使用這些記錄的 Blob 儲存體中。 您會看到搜尋流量記錄的深入解析-**記錄-insights-logs-operationlogs**容器，以及計量的**見解-計量 pt1m**容器。

1. 在[Power BI Apps marketplace](https://appsource.microsoft.com/marketplace/apps)中尋找 Azure 認知搜尋 Power BI 應用程式，並將它安裝到新的工作區或現有的工作區。 此應用程式稱為「 **Azure 認知搜尋」：分析記錄和計量**。

1. 安裝應用程式之後，請從 Power BI 的應用程式清單中選取應用程式。

    ![](media/search-monitor-logs-powerbi/azure-search-app-tile.png)

1. 選取 **[** 連線] 以連接您的資料

    ![](media/search-monitor-logs-powerbi/get-started-with-your-new-app.png)

1. 輸入包含您記錄和計量的儲存體帳戶名稱。 根據預設，應用程式會查看過去10天的資料，但可以使用**days**參數來變更此值。

    ![](media/search-monitor-logs-powerbi/connect-to-storage-account.png)

1. 選取 [**金鑰**] 作為驗證方法，並提供您的儲存體帳戶金鑰。 選取 [**私人**] 作為 [隱私權等級]。 按一下 [登入] 開始載入程序。

    ![](media/search-monitor-logs-powerbi/connect-to-storage-account-step-two.png)

1. 等待資料重新整理。 視您擁有的資料量而定，這可能需要一些時間。 您可以根據下列指標查看資料是否仍會重新整理。

    ![](media/search-monitor-logs-powerbi/workspace-view-refreshing.png)

1. 資料重新整理完成後，請選取 [ **Azure 認知搜尋報告**] 來查看報表。

    ![](media/search-monitor-logs-powerbi/workspace-view-select-report.png)

1. 請務必在開啟報表後重新整理頁面，讓它與您的資料一起開啟。

    ![](media/search-monitor-logs-powerbi/powerbi-search.png)

## <a name="how-to-change-the-app-parameters"></a>如何變更應用程式參數
如果您想要將不同儲存體帳戶的資料視覺化，或變更要查詢的資料天數，請遵循下列步驟來變更**days**和**StorageAccount**參數。

1. 流覽至您的 Power BI 應用程式，尋找您的 Azure 認知搜尋應用程式，然後選取 [**編輯應用程式**] 按鈕以查看工作區。

    ![](media/search-monitor-logs-powerbi/azure-search-app-tile-edit.png)

1. 從資料集選項中選取 [**設定**]。

    ![](media/search-monitor-logs-powerbi/workspace-view-select-settings.png)

1. 在 [資料集] 索引標籤中，變更參數值，**然後選取 [** 套用]。 如果連接發生問題，請在相同的頁面上更新資料來源認證。

1. 流覽回到工作區，然後從資料集選項中選取 [**立即**重新整理]。

    ![](media/search-monitor-logs-powerbi/workspace-view-select-refresh-now.png)

1. 開啟報表以查看更新的資料。 您可能也需要重新整理報表，以查看最新的資料。

## <a name="troubleshooting"></a>疑難排解
如果您發現您看不到您的資料，請遵循下列疑難排解步驟：

1. 開啟報表並重新整理頁面，以確定您正在查看最新的資料。 報表中有一個選項可重新整理資料。 選取此，以取得最新的資料。

1. 請確定您提供的儲存體帳戶名稱和存取金鑰正確。 儲存體帳戶名稱應該對應至您的搜尋服務記錄所設定的帳戶。

1. 確認您的儲存體帳戶包含容器**見解-記錄-insights-logs-operationlogs**和**深入解析-計量-pt1m** ，且每個容器都有資料。 記錄和計量會在幾層的資料夾內。

1. 請檢查資料集是否仍在重新整理中。 重新整理狀態指標會顯示在上述步驟8中。 如果仍在重新整理，請等到重新整理完成後，再開啟並重新整理報表。

## <a name="next-steps"></a>後續步驟
[深入瞭解 Azure 認知搜尋](https://docs.microsoft.com/azure/search/)

[Power BI 是什麼？](https://docs.microsoft.com/power-bi/fundamentals/power-bi-overview)

[Power BI 服務中的設計工具基本概念](https://docs.microsoft.com/power-bi/service-basic-concepts)