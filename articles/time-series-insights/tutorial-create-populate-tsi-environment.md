---
title: 教學課程：建立環境 - Azure 時間序列深入解析 | Microsoft Docs
description: 了解如何建立已從模擬裝置填入資料的時間序列深入解析環境。
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 7bebc9e682f5156fa235b77ff020e502695a28be
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2020
ms.locfileid: "76981186"
---
# <a name="tutorial-create-an-azure-time-series-insights-environment"></a>教學課程：建立 Azure 時間序列深入解析環境

本教學課程將逐步引導您建立已從模擬裝置填入資料的 Azure 時間序列深入解析環境。 在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立時間序列深入解析環境。
> * 建立包含 IoT 中樞的裝置模擬解決方案。
> * 將時間序列深入解析環境連線到 IoT 中樞。
> * 執行裝置模擬以將資料串流處理到時間序列深入解析環境中。
> * 驗證模擬的遙測資料。

> [!IMPORTANT]
> 如果您沒有帳戶，請註冊[免費 Azure 訂用帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

* 您的 Azure 登入帳戶也必須是訂用帳戶的**擁有者**角色成員。 如需詳細資訊，請閱讀[使用角色型存取控制和 Azure 入口網站管理存取權](../role-based-access-control/role-assignments-portal.md)。

## <a name="review-video"></a>檢閱影片

### <a name="learn-how-to-use-an-azure-iot-solution-accelerator-to-generate-data-and-get-started-with-time-series-insights-br"></a>了解如何使用「Azure IoT 解決方案加速器」產生資料並開始使用「時間序列深入解析」。 </br>

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="overview"></a>概觀

時間序列深入解析環境是收集及儲存裝置資料之處。 儲存資料後，[Azure 時間序列深入解析總管](time-series-quickstart.md)和[時間序列深入解析查詢 API](/rest/api/time-series-insights/ga-query-api) 即可用來查詢及分析資料。

Azure IoT 中樞是教學課程中所有裝置 (模擬或實體) 用來安全地連線至 Azure 雲端並傳輸資料的事件來源。

本教學課程也會使用 [IoT 解決方案加速器](https://www.azureiotsolutions.com)，以產生範例遙測資料並串流處理至 IoT 中樞。

>[!TIP]
> [IoT 解決方案加速器](https://www.azureiotsolutions.com)提供預先設定的企業級解決方案，可讓您用來加快自訂 IoT 解決方案的開發速度。

## <a name="create-a-device-simulation"></a>建立裝置模擬

首先，請建立裝置模擬解決方案，此解決方案將產生測試資料以填入您的時間序列深入解析環境中。

1. 在個別視窗或索引標籤中，移至 [azureiotsolutions.com](https://www.azureiotsolutions.com)。 使用相同的 Azure 訂用帳戶登入，然後選取 [裝置模擬]  加速器。

   [![執行裝置模擬加速器](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-landing-page.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-landing-page.png#lightbox)

1.  選取 [立即試用]  。 然後，在 [建立裝置模擬解決方案]  頁面上輸入必要的參數。

   參數|描述
   ---|---
   **部署名稱** | 此唯一值會用來建立新的資源群組。 列出的 Azure 資源會在建立後指派給資源群組。
   **Azure 訂用帳戶** | 指定與上一節中用於建立「時間序列深入解析」環境的相同訂用帳戶。
   **部署選項** | 選取 [佈建新的 IoT 中樞]  以建立本教學課程特定的新 IoT 中樞。
   **Azure 位置** | 指定與上一節中用於建立「時間序列深入解析」環境的相同區域。

   完成後，請選取 [建立]  以佈建解決方案的 Azure 資源。 完成此程序約需要 20 分鐘的時間。

   [![佈建裝置模擬解決方案](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-configuration.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-configuration.png#lightbox)

1. 佈建完成後會顯示兩項更新，指出部署狀態已從 [佈建中]  變為 [就緒]  。 

   >[!IMPORTANT]
   > 請還不要輸入解決方案加速器！ 請將此網頁保持為開啟，因為您稍後將會回到這裡。

   [![裝置模擬解決方案佈建完成](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png#lightbox)

1. 現在，您可以在 Azure 入口網站中檢查新建立的資源。 在 [資源群組]  頁面中，您會看到已建立的新資源群組，其使用上一個步驟中提供的**解決方案名稱**。 記下針對裝置模擬所建立的資源。

   [![裝置模擬資源](media/tutorial-create-populate-tsi-environment/tsi-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/tsi-device-sim-solution-resources.png#lightbox)

## <a name="create-an-environment"></a>建立環境

其次，在您的 Azure 訂用帳戶中建立時間序列深入解析環境。

1. 使用 Azure 訂用帳戶登入 [Azure 入口網站](https://portal.azure.com)。 
1. 選取左上方的 [+ 建立資源]  。 
1. 選取 [物聯網]  類別，然後選取 [時間序列深入解析]  。 

   [![選取時間序列深入解析環境資源](media/tutorial-create-populate-tsi-environment/tsi-create-new-environment.png)](media/tutorial-create-populate-tsi-environment/tsi-create-new-environment.png#lightbox)

1. 在 [時間序列深入解析環境]  頁面上，填入必要參數。

   參數|描述
   ---|---
   **環境名稱** | 為時間序列深入解析環境選擇唯一的名稱。 時間序列深入解析總管和[查詢 API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query) 會使用這些名稱。
   **訂用帳戶** | 訂用帳戶是 Azure 資源的容器。 請選擇要建立時間序列深入解析環境的訂用帳戶。
   **資源群組** | 資源群組是 Azure 資源的容器。 請為時間序列深入解析環境資源選擇現有的資源群組，或建立新的群組。
   **位置** | 為時間序列深入解析環境選擇資料中心區域。 為了避免額外延遲時間，請在與其他 IoT 資源相同的區域中建立時間序列深入解析環境。
   **層級** | 選擇所需的輸送量。 選取 [S1]  。
   **容量** | 容量是套用至與所選取 SKU 相關聯輸入速率和儲存體容量的乘數。 您可以在建立之後變更容量。 選取 **1** 作為容量。

   完成後，選取 [下一步：  事件來源] 以繼續進行下一個步驟。

   [![建立時間序列深入解析環境資源](media/tutorial-create-populate-tsi-environment/tsi-create-resource-tsi-params.png)](media/tutorial-create-populate-tsi-environment/tsi-create-resource-tsi-params.png#lightbox)

1. 現在，將時間序列深入解析環境連線到解決方案加速器建立的 IoT 中樞。 將 [選取中樞]  設定為 `Select existing`。 然後，在設定 [IoT 中樞名稱]  時，選擇解決方案加速器建立的 IoT 中樞。

   [![將時間序列深入解析環境連線到已建立的 IoT 中樞](media/tutorial-create-populate-tsi-environment/tsi-create-resource-iot-hub.png)](media/tutorial-create-populate-tsi-environment/tsi-create-resource-iot-hub.png#lightbox)

   最後，選取 [檢閱 + 建立]  。

1. 查看 [通知]  面板以監視部署完成進度。 

   [![時間序列深入解析環境部署成功](media/tutorial-create-populate-tsi-environment/create-resource-tsi-deployment-succeeded.png)](media/tutorial-create-populate-tsi-environment/create-resource-tsi-deployment-succeeded.png#lightbox)

## <a name="run-device-simulation"></a>執行裝置模擬

既然部署和初始設定已完成，請將[加速器建立的模擬裝置](#create-a-device-simulation)的範例資料填入時間序列深入解析環境。

連同 IoT 中樞，也產生了 Azure App Service Web 應用程式，以建立及傳輸模擬裝置遙測資料。

1. 返回您的[解決方案加速器儀表板](https://www.azureiotsolutions.com/Accelerators#dashboard)。 如有必要，請使用您在本教學課程中使用的相同 Azure 帳戶重新登入。 選取您的「裝置解決方案」，然後**移至您的解決方案加速器**，以啟動您已部署的解決方案。

   [![解決方案加速器儀表板](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png#lightbox)

1. 裝置模擬 Web 應用程式會從提示您把「將您登入並讀取您的設定檔」  的權限授與 Web 應用程式開始。 此權限可讓應用程式擷取支援應用程式運作所需的使用者設定檔資訊。

   [![裝置模擬 Web 應用程式同意](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png#lightbox)

1. 選取 [+ 新增模擬]  。 在 [模擬設定]  頁面載入後，請輸入必要的參數。

   參數|描述
   ---|---
   **目標 IoT 中樞** | 選取 [使用預先佈建的 IoT 中樞]  。
   **裝置型號** | 選取 [Chiller]  。
   **裝置數目**  | 在 [數量]  下輸入 `10`。
   **遙測頻率** | 輸入 `10` 秒。
   **模擬持續時間** | 選取 [結束時間：]  ，然後輸入 `5` 分鐘。

   完成之後，選取 [開始模擬]  。 模擬執行時間共 5 分鐘。 此模擬每 10 秒會從 1,000 部模擬裝置產生資料。 

   [![裝置模擬設定](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png#lightbox)

1. 模擬執行時，您會注意到 [訊息總數]  和 [每秒訊息數]  欄位大約每隔 10 秒會更新一次。 模擬大約會在 5 分鐘後結束，並且返回**模擬設定**。

   [![裝置模擬執行中](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png#lightbox)

## <a name="verify-the-telemetry-data"></a>驗證遙測資料

在最後的這一節中，您會驗證遙測資料是否已產生並儲存在時間序列深入解析環境中。 若要驗證資料，您可以使用 [時間序列深入解析] 總管來查詢和分析遙測資料。

1. 返回時間序列深入解析環境的資源群組 [概觀]  頁面。 選取 Time Series Insights 環境。

   [![時間序列深入解析環境資源群組和環境](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

1. 在時間序列深入解析環境的 [概觀]  頁面上，選取 [時間序列深入解析總管 URL]  ，以開啟時間序列深入解析總管。

   [![時間序列深入解析總管](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

1. 時間序列深入解析總管會使用您的 Azure 入口網站帳戶進行載入和驗證。 最初，會顯示時間序列深入解析環境中已填入模擬遙測資料的圖表區域。 若要篩選為較小的時間範圍，請選取左上角的下拉式清單。 輸入足以跨越裝置模擬持續時間的時間範圍。 然後選取搜尋放大鏡。

   [![時間序列深入解析總管時間範圍篩選條件](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

1. 縮小時間範圍，可讓圖表放大顯示傳輸至 IoT 中樞與時間序列深入解析環境的資料明顯暴增。 同時也請留意右上角的**串流處理完成**文字，此處會顯示找到的事件總數。 您也可以拖曳 [間隔大小]  滑桿，來控制圖表的繪圖細微性。

   [![時間序列深入解析總管時間範圍篩選後檢視](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

1. 最後，您也可以對某個區域按一下滑鼠左鍵來篩選範圍。 然後按一下滑鼠右鍵，使用**瀏覽事件**在表格式 [事件]  檢視中顯示事件的詳細資料。

   [![時間序列深入解析總管時間範圍篩選後檢視和事件](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>清除資源

本教學課程建立了數個執行中的 Azure 服務，用以支援時間序列深入解析環境和裝置模擬解決方案。 若要移除，請瀏覽回到 Azure 入口網站。

從 Azure 入口網站左側的功能表中：

1. 選取 [所有資源]  圖示。 然後選取您為時間序列深入解析環境建立的資源群組。 在頁面頂端選取 [刪除資源群組]  、輸入資源群組的名稱，然後選取 [刪除]  。

1. 選取 [所有資源]  圖示。 然後選取裝置模擬解決方案加速器所建立的資源群組。 在頁面頂端選取 [刪除資源群組]  、輸入資源群組的名稱，然後選取 [刪除]  。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 建立時間序列深入解析環境。
> * 建立包含 IoT 中樞的裝置模擬解決方案。
> * 將時間序列深入解析環境連線到 IoT 中樞。
> * 執行裝置模擬以將資料串流處理到時間序列深入解析環境中。
> * 驗證模擬的遙測資料。

您現在已了解如何建立您自己的時間序列深入解析環境，並了解如何建置從時間序列深入解析環境中取用資料的 Web 應用程式：

> [!div class="nextstepaction"]
> [閱讀裝載的用戶端 SDK 虛擬化範例](https://tsiclientsample.azurewebsites.net/)
