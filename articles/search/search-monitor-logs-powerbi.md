---
title: 使用強力 BI 視覺化 Azure 認知搜索日誌和指標
description: 使用強力 BI 視覺化 Azure 認知搜索日誌和指標
manager: eladz
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: 618033e24236d26601fafaf7b0678ee83ec973e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650130"
---
# <a name="visualize-azure-cognitive-search-logs-and-metrics-with-power-bi"></a>使用強力 BI 視覺化 Azure 認知搜索日誌和指標
[Azure 認知搜索](https://docs.microsoft.com/azure/search/search-what-is-azure-search)允許您在 Azure 存儲帳戶中存儲有關搜索服務的動作記錄和服務指標。 本頁提供有關如何通過 Power BI 範本應用視覺化該資訊的說明。 該應用程式提供有關搜索服務的詳細見解，包括有關搜索、索引、操作和服務指標的資訊。

您可以在[Power BI 應用市場](https://appsource.microsoft.com/marketplace/apps)找到 Power BI 範本應用**Azure 認知搜索：分析日誌和指標**。

## <a name="how-to-get-started-with-the-app"></a>如何開始使用應用程式
1. 為搜索服務啟用診斷日誌記錄：
    1. 創建或標識現有的[Azure 存儲帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)，您可以在其中存檔日誌
    1. 在 Azure 門戶中導航到 Azure 認知搜索服務
    1. 在左列的"監視"部分下，選擇 **"診斷設置"**

        ![](media/search-monitor-logs-powerbi/diagnostic-settings.png)

    1. 選擇 **+ 添加診斷設置**
    1. 檢查**存儲帳戶的存檔**，提供存儲帳戶資訊，並檢查**動作記錄****和所有指標**

        ![](media/search-monitor-logs-powerbi/add-diagnostic-setting.png)
    1. 選擇 **"保存"**

1. 啟用日誌記錄後，使用搜索服務開始生成日誌和指標。 容器最多需要一個小時才能與這些日誌一起顯示在 Blob 存儲中。 您將看到用於搜索流量**日誌的見解日誌動作記錄**容器和用於指標的**見解指標-pt1m**容器。

1. 在[Power BI 應用市場](https://appsource.microsoft.com/marketplace/apps)中查找 Azure 認知搜索電源 BI 應用，並將其安裝到新工作區或現有工作區中。 該應用程式稱為**Azure 認知搜索：分析日誌和指標**。

1. 安裝應用後，從 Power BI 中的應用清單中選擇應用。

    ![](media/search-monitor-logs-powerbi/azure-search-app-tile.png)

1. 選擇 **"連接"** 以連接資料

    ![](media/search-monitor-logs-powerbi/get-started-with-your-new-app.png)

1. 輸入包含日誌和指標的存儲帳戶的名稱。 預設情況下，應用將查看最近 10 天的資料，但可以使用 **"天"** 參數更改此值。

    ![](media/search-monitor-logs-powerbi/connect-to-storage-account.png)

1. 選擇 **"金鑰**"作為身份驗證方法並提供存儲帳戶金鑰。 選擇 **"專用**"作為隱私級別。 按一下 [登入] 開始載入程序。

    ![](media/search-monitor-logs-powerbi/connect-to-storage-account-step-two.png)

1. 等待刷新資料。 這可能需要一些時間，具體取決於您擁有的資料量。 您可以根據以下指標查看資料是否仍在刷新。

    ![](media/search-monitor-logs-powerbi/workspace-view-refreshing.png)

1. 資料刷新完成後，選擇 Azure**認知搜索報表**以查看報表。

    ![](media/search-monitor-logs-powerbi/workspace-view-select-report.png)

1. 請確保在打開報表後刷新頁面，以便該頁隨資料一起打開。

    ![](media/search-monitor-logs-powerbi/powerbi-search.png)

## <a name="how-to-change-the-app-parameters"></a>如何更改應用參數
如果要視覺化來自其他存儲帳戶的資料或更改要查詢的資料天數，請按照以下步驟更改 **"天"** 和 **"存儲帳戶"** 參數。

1. 導航到 Power BI 應用，查找 Azure 認知搜索應用，然後選擇 **"編輯應用**"按鈕以查看工作區。

    ![](media/search-monitor-logs-powerbi/azure-search-app-tile-edit.png)

1. 從"資料集"選項中選擇 **"設置**"。

    ![](media/search-monitor-logs-powerbi/workspace-view-select-settings.png)

1. 在"資料集"選項卡中，更改參數值並選擇"**應用**"。 如果連接出現問題，請更新同一頁上的資料來源憑據。

1. 導航回工作區，然後從"資料集"選項中選擇 **"立即刷新**"。

    ![](media/search-monitor-logs-powerbi/workspace-view-select-refresh-now.png)

1. 打開報表以查看更新的資料。 您可能還需要刷新報表才能查看最新資料。

## <a name="troubleshooting"></a>疑難排解
如果您發現看不到資料，請按照以下故障排除步驟操作：

1. 打開報表並刷新頁面，以確保查看最新資料。 報表中有一個選項用於刷新資料。 選擇此選項可獲取最新資料。

1. 確保您提供的存儲帳戶名稱和訪問金鑰正確無誤。 存儲帳戶名稱應對應于與搜索服務日誌一起配置的帳戶。

1. 確認存儲帳戶包含容器**見解-日誌-動作記錄**和**見解指標-pt1m，** 每個容器都有資料。 日誌和指標將位於幾個資料夾層內。

1. 檢查資料集是否仍在刷新。 刷新狀態指示器顯示在上面的步驟 8 中。 如果仍處於刷新狀態，請等待刷新完成以打開並刷新報表。

## <a name="next-steps"></a>後續步驟
[瞭解有關 Azure 認知搜索的更多資訊](https://docs.microsoft.com/azure/search/)

[Power BI 是什麼？](https://docs.microsoft.com/power-bi/fundamentals/power-bi-overview)

[Power BI 服務中的設計工具基本概念](https://docs.microsoft.com/power-bi/service-basic-concepts)