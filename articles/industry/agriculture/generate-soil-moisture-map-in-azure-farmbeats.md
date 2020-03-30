---
title: 生成土壤水分熱圖
description: 描述如何在 Azure 農場節拍中生成土壤水分熱圖
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: a2115e9c1601c86cce8857c10baf12b91cc2b997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482565"
---
# <a name="generate-soil-moisture-heatmap"></a>生成土壤水分熱圖

土壤水分是土壤顆粒之間的空間中持有的水。土壤水分熱圖可説明您瞭解任何深度和農場內高解析度的水分資料。 要生成準確且可用的土壤濕度熱圖，需要統一部署來自同一供應商的感應器。 不同的供應商在測量土壤水分的方式上存在差異，同時校準也存在差異。 使用部署于該深度的感應器為特定深度生成熱圖。

本文介紹使用 Azure FarmBeats 加速器為伺服器場生成土壤水分熱圖的過程。 在本文中，您將了解如何：

- [創建伺服器場](#create-a-farm)
- [將感應器分配給伺服器場](#get-soil-moisture-sensor-data-from-partner)
- [生成土壤水分熱圖](#generate-soil-moisture-heatmap)

## <a name="before-you-begin"></a>開始之前

確認下列事項：  

- Azure 訂用帳戶。
- Azure 農場節拍的運行實例。
- 農場至少提供三個土壤水分感應器。

## <a name="create-a-farm"></a>建立伺服器陣列

農場是一個感興趣的地理區域，您要為此創建土壤水分熱圖。 您可以使用[伺服器場 API](https://aka.ms/FarmBeatsDatahubSwagger)或在[伺服器場節拍加速器 UI](manage-farms-in-azure-farmbeats.md#create-farms)中創建伺服器場

## <a name="deploy-sensors"></a>部署感應器

您應該在農場上物理部署土壤濕度感應器。 你可以從我們批准的任何合作夥伴 -[大衛斯儀器](https://www.davisinstruments.com/product/enviromonitor-gateway/)和[太溶物](https://teralytic.com/)購買土壤水分感應器。 您應該與感應器提供程式協調，以在伺服器場上執行物理設置。

## <a name="get-soil-moisture-sensor-data-from-partner"></a>從合作夥伴處獲取土壤水分感應器資料

當感應器開始資料流資料時，資料將啟用到 Azure FarmBeats 中。 這可以從合作夥伴應用程式完成。

例如，如果您購買了 Davis 感應器，您將登錄到天氣連結帳戶，並提供所需的憑據以啟用資料流程式傳輸到 Azure FarmBeats 中。 要獲取所需的憑據，請按照[獲取感應器資料](get-sensor-data-from-sensor-partner.md#get-sensor-data-from-sensor-partners)中的說明進行操作。

輸入憑據並在合作夥伴應用程式中選擇 **"提交"** 後，可以將資料流程入 Azure FarmBeats 中。

### <a name="assign-soil-moisture-sensors-to-the-farm"></a>將土壤水分感應器分配給農場

將感應器帳戶連結到 Azure FarmBeats 後，需要將土壤濕度感應器分配給感興趣的伺服器場。

1.  在主頁中，從功能表中選擇 **"伺服器場**"，將顯示 **"伺服器場"** 清單頁。
2.  選擇 **"我的農場** > **添加設備**"。
3.  將顯示"**添加設備"** 視窗。 選擇與農場土壤濕度感應器連結的任何設備。

    ![專案 FarmBeats](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. 選擇 **"添加設備**"。     

## <a name="generate-soil-moisture-heatmap"></a>生成土壤水分熱圖

此步驟是創建作業或長時間運行的操作，為您的農場生成土壤水分熱圖。

1.  在主頁上，從左側導航功能表轉到 **"伺服器場"** 以查看伺服器場頁面。
2.  選擇 **"我的農場**"。
3.  在 **"伺服器場詳細資訊"** 頁中，選擇 **"生成精度映射**"。
4.  從下拉式功能表中，選擇**土壤水分**。
5.  在**土壤濕度**視窗中，選擇**本周**。
6.  在 **"選擇土壤水分****感應器度量"中**，輸入要用於地圖的度量值。
    要查找感應器測量值，請在**感應器**中選擇任何土壤濕度感應器。 在 **"感應器屬性**"中，使用**度量值名稱**值。

    ![專案 FarmBeats](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)


7.  選擇 **"生成地圖**"。
    將顯示一條帶有作業詳細資訊的確認訊息。 有關詳細資訊，請參閱作業中的作業狀態。

    >[!NOTE]
    > 這項工作大約需要三到四個小時才能完成。

### <a name="download-the-soil-moisture-heatmap"></a>下載土壤水分熱圖

使用下列步驟：

1. 在 **"作業"** 頁中，檢查上次過程中創建的作業的**作業狀態**。
2. 當作業狀態顯示 **"成功"** 時，請在功能表上選擇 **"地圖**"。
3. 在以土壤-moisture_MyFarm_YYYY-MM-DD><創建地圖的當天搜索地圖。
4. 在 **"名稱"** 列中選擇地圖，將顯示一個快顯視窗，並預覽所選地圖。
5. 選擇**下載**。 地圖將下載並存儲到電腦的本地資料夾。

    ![專案 FarmBeats](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)

## <a name="next-steps"></a>後續步驟

現在，您已成功生成土壤水分熱圖，瞭解如何[生成感應器放置](generate-maps-in-azure-farmbeats.md#sensor-placement-map)和[引入歷史遙測資料](ingest-historical-telemetry-data-in-azure-farmbeats.md)。 
