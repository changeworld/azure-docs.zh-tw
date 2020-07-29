---
title: 產生土料濕度熱度圖
description: 說明如何在 Azure FarmBeats 中產生泥土濕度熱度圖
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: a2115e9c1601c86cce8857c10baf12b91cc2b997
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75482565"
---
# <a name="generate-soil-moisture-heatmap"></a>產生土料濕度熱度圖

土裡濕度是在上料粒子之間保留的水。內料濕度熱度圖可協助您瞭解任何深度的濕度資料，以及您的伺服器陣列中的高解析度。 為了產生準確且可用的土料濕度熱度圖，必須從相同的提供者進行感應器的一致部署。 不同的提供者會在測量上料濕度的方式上有差異，以及校正的差異。 使用在該深度部署的感應器，針對特定深度產生熱度圖。

本文說明如何使用 Azure FarmBeats 加速器，為您的伺服器陣列產生土料濕度熱度圖的程式。 在本文中，您將了解如何：

- [建立伺服器陣列](#create-a-farm)
- [將感應器指派給伺服器陣列](#get-soil-moisture-sensor-data-from-partner)
- [產生土料濕度熱度圖](#generate-soil-moisture-heatmap)

## <a name="before-you-begin"></a>開始之前

確認下列事項：  

- Azure 訂用帳戶。
- 正在執行的 Azure FarmBeats 實例。
- 伺服器陣列可使用最少三個上料濕度感應器。

## <a name="create-a-farm"></a>建立伺服器陣列

伺服器陣列是您想要為其建立土料濕度熱度圖的地理區域。 您可以使用[伺服器陣列 API](https://aka.ms/FarmBeatsDatahubSwagger)或[FarmsBeats 加速器 UI](manage-farms-in-azure-farmbeats.md#create-farms)來建立伺服器陣列

## <a name="deploy-sensors"></a>部署感應器

您應該在伺服器陣列上實際部署土裡濕度感應器。 您可以從我們核准的合作夥伴- [Davis 儀器](https://www.davisinstruments.com/product/enviromonitor-gateway/)和[Teralytic](https://teralytic.com/)購買土裡的濕度感應器。 您應該與感應器提供者協調，以在伺服器陣列上執行實體設定。

## <a name="get-soil-moisture-sensor-data-from-partner"></a>取得來自合作夥伴的土濕度感應器資料

當感應器開始串流處理時，資料會進入 [合作夥伴資料儀表板]，並將資料啟用到 Azure FarmBeats。 這可以透過合作夥伴應用程式來完成。

例如，如果您已購買 Davis 感應器，您會登入氣象連結帳戶，並提供必要的認證，以將資料串流至 Azure FarmBeats。 若要取得所需的認證，請遵循[取得感應器資料](get-sensor-data-from-sensor-partner.md#get-sensor-data-from-sensor-partners)中的指示。

一旦您輸入認證並選取合作夥伴應用程式上的 [**提交**]，您就可以讓資料流程入 Azure FarmBeats。

### <a name="assign-soil-moisture-sensors-to-the-farm"></a>將土裡濕度感應器指派給伺服器陣列

將您的感應器帳戶連結至 Azure FarmBeats 之後，您必須將「土裡濕度感應器」指派給感對的伺服器陣列。

1.  在首頁中，從功能表中選取 [**伺服器**陣列]，隨即會顯示 [**伺服器**陣列清單] 頁面。
2.  選取 [ **MyFarm**] [  >  **新增裝置**]。
3.  [**新增裝置**] 視窗隨即顯示。 選取連結至您伺服器陣列之土濕度感應器的任何裝置。

    ![專案 FarmBeats](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. 選取 [**新增裝置**]。     

## <a name="generate-soil-moisture-heatmap"></a>產生土料濕度熱度圖

此步驟是建立一或長時間執行的作業，以產生您伺服器陣列的土濕度熱度圖。

1.  在 [首頁] 頁面上，移至左側導覽功能表中的 [**伺服器**陣列]，以查看 [伺服器陣列] 頁面。
2.  選取 [ **MyFarm**]。
3.  在 [**伺服器陣列詳細資料**] 頁面中，選取 [**產生精確度對應**]。
4.  從下拉式功能表中，選取 [土裡**濕度**]。
5.  在 [**土**裡的濕度] 視窗中，選取 [**本周**]。
6.  在 [**選取內建濕度****感應器量值**] 中，輸入您想要用於對應的量值。
    若要找出感應器量值，請在 [**感應器**] 中選取任何土裡的濕度感應器。 在 [**感應器屬性**] 中，使用 [**量值名稱**] 值。

    ![專案 FarmBeats](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)


7.  選取 [**產生對應**]。
    隨即顯示含有作業詳細資料的確認訊息。 如需詳細資訊，請參閱作業中的作業狀態。

    >[!NOTE]
    > 作業大約需要三到四個小時才能完成。

### <a name="download-the-soil-moisture-heatmap"></a>下載土裡濕度熱度圖

請使用下列步驟：

1. 在 [**作業**] 頁面中，檢查您在上一個程式中建立之作業的**作業狀態**。
2. 當 [作業狀態] 顯示為 [**成功**] 時，請選取功能表上的 [**對應**]。
3. 依 <土 moisture_MyFarm_YYYY-MM-DD> 的格式，搜尋其建立日期的對應。
4. 在 [**名稱**] 資料行中選取一個對應，就會顯示一個快顯視窗，其中包含所選取對應的預覽。
5. 選取 [下載]。 對應會下載並儲存到您電腦的本機資料夾。

    ![專案 FarmBeats](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)

## <a name="next-steps"></a>後續步驟

既然您已成功產生一個土裡濕度熱度圖，請瞭解如何[產生感應器放置](generate-maps-in-azure-farmbeats.md#sensor-placement-map)和內嵌歷程[記錄遙測資料](ingest-historical-telemetry-data-in-azure-farmbeats.md)。 
