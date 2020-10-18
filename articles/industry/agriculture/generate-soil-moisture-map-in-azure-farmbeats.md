---
title: 產生泥土濕度熱度圖
description: 說明如何在 Azure FarmBeats 中產生中的泥土濕度熱度圖
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 2887e7c5fd875942c3d19391267ddbb9b73ccfec
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2020
ms.locfileid: "92165548"
---
# <a name="generate-soil-moisture-heatmap"></a>產生泥土濕度熱度圖

內建的濕度是在每個上料的粒子之間的空格中所持有的水。您可以在伺服器陣列內的任何深度和高解析度上，以熱度圖的方式來瞭解濕度資料。 若要產生精確且可使用的泥土濕度熱度圖，需要相同提供者的感應器一致部署。 不同的提供者會有不同的測量方式，也就是在校正的差異方面測量像是一種。 熱度圖會使用在該深度部署的感應器來產生特定深度。

本文說明如何使用 Azure FarmBeats 加速器，為您的伺服器陣列產生內料濕度熱度圖的流程。 在本文中，您將了解如何：

- [建立伺服器陣列](#create-a-farm)
- [將感應器指派給伺服器陣列](#get-soil-moisture-sensor-data-from-partner)
- [產生泥土濕度熱度圖](#generate-soil-moisture-heatmap)

## <a name="before-you-begin"></a>開始之前

確認下列事項：  

- Azure 訂用帳戶。
- 正在執行的 Azure FarmBeats 實例。
- 伺服陣列有三個最少的上料濕度感應器。

## <a name="create-a-farm"></a>建立伺服器陣列

伺服器陣列是您想要為其建立一張中料濕度熱度圖的地理區域。 您可以使用[伺服器陣列 API](https://aka.ms/FarmBeatsDatahubSwagger)或[FarmsBeats 加速器 UI](manage-farms-in-azure-farmbeats.md#create-farms)來建立伺服器陣列

## <a name="deploy-sensors"></a>部署感應器

您應該在伺服器陣列上實際部署土裡濕度感應器。 您可以從我們核准的合作夥伴 [Davis 儀器](https://www.davisinstruments.com/product/enviromonitor-gateway/) 和 [>teralytic](https://teralytic.com/)購買全像濕度感應器。 您應協調感應器提供者，以在您的伺服器陣列上進行實體設定。

## <a name="get-soil-moisture-sensor-data-from-partner"></a>取得來自合作夥伴的泥土濕度感應器資料

當感應器開始將資料串流處理至夥伴資料儀表板時，它們會將資料啟用至 Azure FarmBeats。 這可以從夥伴應用程式完成。

例如，如果您已購買 Davis 感應器，您將會登入氣象連結帳戶，並提供必要的認證，以啟用資料串流至 Azure FarmBeats。 若要取得所需的認證，請依照 [取得感應器資料](get-sensor-data-from-sensor-partner.md#get-sensor-data-from-sensor-partners)中的指示進行。

輸入您的認證並選取合作夥伴應用程式上的 [ **提交** ] 之後，您就可以讓資料流程入 Azure FarmBeats。

### <a name="assign-soil-moisture-sensors-to-the-farm"></a>將上料濕度感應器指派給伺服器陣列

將您的感應器帳戶連結至 Azure FarmBeats 後，您必須將您的上料濕度感應器指派給感興趣的伺服器陣列。

1.  在首頁中，從功能表中選取 [ **伺服器** 陣列]，隨即顯示 [ **伺服器** 陣列] 清單頁面。
2.  選取 [ **MyFarm**  >  **新增裝置**]。
3.  [ **新增裝置** ] 視窗隨即顯示。 選取連結至您的伺服陣列的上料濕度感應器的任何裝置。

    ![顯示 [新增裝置] 畫面的螢幕擷取畫面。](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. 選取 [ **新增裝置**]。     

## <a name="generate-soil-moisture-heatmap"></a>產生泥土濕度熱度圖

此步驟是建立工作或長時間執行的作業，以產生您的伺服陣列的泥土濕度熱度圖。

1.  在首頁上，移至左側導覽功能表中的 [ **伺服器** 陣列]，以查看 [伺服器陣列] 頁面。
2.  選取 [ **MyFarm**]。
3.  在 [ **伺服器陣列詳細資料** ] 頁面中，選取 [ **產生精確度對應**]。
4.  從下拉式功能表中，選取 [上 **料濕度**]。
5.  在 [我的全像 **濕度** ] 視窗中，選取 [ **本周**]。
6.  在 [**選取上料濕度****感應器] 量值**中，輸入您想要用於地圖的量值。
    若要找出感應器量值，請在 [ **感應器**] 中選取任何的泥土濕度感應器。 在 [ **感應器屬性**] 中，使用 [ **量值名稱** ] 值。

    ![顯示中料濕度畫面的螢幕擷取畫面。](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)


7.  選取 [ **產生對應**]。
    隨即會顯示具有作業詳細資料的確認訊息。 如需詳細資訊，請參閱工作中的作業狀態。

    >[!NOTE]
    > 作業大約需要三到四個小時才能完成。

### <a name="download-the-soil-moisture-heatmap"></a>下載熱度圖的泥土濕度

使用下列步驟：

1. 在 [ **作業** ] 頁面中，檢查您在上一個程式中建立之作業的 **作業狀態** 。
2. 當作業狀態顯示為 [ **成功**] 時，請選取功能表上的 [ **對應** ]。
3. 在地圖的建立日期中搜尋地圖，格式為 <中 moisture_MyFarm_YYYY>。
4. 在 [ **名稱** ] 資料行中選取對應，隨即顯示具有所選取地圖預覽的快顯視窗。
5. 選取 [下載]。 地圖會下載並儲存到您電腦的本機資料夾。

    ![專案 FarmBeats](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)

## <a name="next-steps"></a>後續步驟

既然您已成功產生一熱度圖，請瞭解如何 [產生感應器放置](generate-maps-in-azure-farmbeats.md#sensor-placement-map) ，並內嵌歷程 [記錄遙測資料](ingest-historical-telemetry-data-in-azure-farmbeats.md)。 
