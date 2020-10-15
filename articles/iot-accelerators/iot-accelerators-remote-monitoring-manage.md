---
title: 在遠端監視解決方案中設定裝置 - Azure | Microsoft Docs
description: 本教學課程會示範如何設定連線到遠端監視解決方案加速器的裝置。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 432386809596fb2ef040a05d1fe0d12294a1abef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91534517"
---
# <a name="tutorial-configure-devices-connected-to-your-monitoring-solution"></a>教學課程：設定連線到監視解決方案的裝置

在本教學課程中，您會使用遠端監視解決方案加速器來設定和管理已連線的 IoT 裝置。 您會在解決方案加速器中新增裝置，以及設定該裝置。

Contoso 已排序新的機制來延伸其中一個設備。 在等待新機制交付時，建議您執行模擬來測試解決方案的行為。 若要執行模擬，您可以在遠端監視解決方案加速器中新增模擬的引擎裝置，並測試此模擬裝置可正確回應組態更新。 雖然本教學課程使用模擬裝置，但裝置開發人員可以在[與遠端監視解決方案加速器連線的真實裝置](iot-accelerators-connecting-devices.md)上實作直接方法。

在本教學課程中，您：

>[!div class="checklist"]
> * 佈建模擬的裝置。
> * 測試模擬裝置。
> * 重新設定裝置。
> * 組織裝置。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="add-a-simulated-device"></a>新增模擬裝置

瀏覽至解決方案中的 [裝置總管]  頁面，然後按一下 [+ 新增裝置]  ：

[![佈建模擬裝置](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-expanded.png#lightbox)

在 [新增裝置]  面板中，選擇 [模擬]  ，讓所要佈建的裝置數目維持 **1** 個，選擇 [故障引擎]  裝置模型，然後選擇 [套用]  以建立模擬裝置：

[![佈建模擬的引擎裝置](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-expanded.png#lightbox)

## <a name="test-the-simulated-device"></a>測試模擬的裝置

若要測試模擬的引擎裝置是否會傳送遙測資料並報告屬性值，請在 [裝置總管]  頁面上的裝置清單中選取該裝置。 引擎的相關即時資訊會顯示在 [裝置詳細資料]  面板中：

[![檢視新的模擬引擎裝置](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-expanded.png#lightbox)

在 [裝置詳細資料]  中，確認新的裝置正在傳送遙測。 若要檢視裝置的震動遙測資料串流，請按一下 [震動]  ：

[![選取要檢視的遙測資料串流](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-expanded.png#lightbox)

[裝置詳細資料]  面板會顯示關於裝置的其他資訊 (例如標記值)、它支援的方法，以及裝置所報告的屬性。

若要檢視詳細的診斷，請在 [裝置詳細資料]  面板中向下捲動，以檢視 [診斷]  區段。

## <a name="reconfigure-a-device"></a>重新設定裝置

若要測試是否可以更新引擎的設定屬性，請在 [裝置總管]  頁面的裝置清單中選取裝置。 接著按一下 [作業]  ，然後選擇 [屬性]  。 [作業] 面板會顯示所選裝置的可更新屬性值：

[![重新設定裝置](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-expanded.png#lightbox)

若要更新引擎的位置，請將作業名稱設定為 [UpdateEngineLocation]  、將經度設定為 [-122.15]  、將位置設定為 [Factory 2]  、將緯度設定為 [47.62]  ，然後按一下 [套用]  ：

[![此螢幕擷取畫面顯示已反白顯示 [工作] 視窗的 [裝置總管] 頁面。](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-expanded.png#lightbox)

若要追蹤作業狀態，請按一下 [檢視作業狀態]  ：

[![更新裝置屬性值](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-inline.png)](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-expanded.png#lightbox)

作業完成後，瀏覽到 [儀表板]  頁面。 引擎裝置會在地圖上顯示於其新的位置：

[![檢視引擎位置](./media/iot-accelerators-remote-monitoring-manage/enginelocation-inline.png)](./media/iot-accelerators-remote-monitoring-manage/enginelocation-expanded.png#lightbox)

## <a name="organize-your-devices"></a>組織裝置

若要讓操作員能更輕鬆地組織及管理您的裝置，您可以使用小組名稱為裝置加上標記。 Contoso 有兩個不同的小組會提供現場服務活動：

* Smart Vehicle 小組會管理卡車和原型裝置。
* Smart Building 小組會管理 chiller、電梯和引擎。

若要顯示所有的裝置，請瀏覽至 [裝置總管]  頁面，然後選擇 [所有裝置]  篩選條件：

[![顯示所有裝置](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-expanded.png#lightbox)

### <a name="add-tags"></a>新增標記

選取所有的**卡車**和**原型**裝置。 然後，按一下 [作業]  。

在 [作業] 面板中選取 [標記]，將作業名稱設定為 **AddConnectedVehicleTag**，然後使用 **ConnectedVehicle** 值新增名為 **FieldService** 的文字標記。 然後，按一下 [套用]  ：

[![將標記新增至原型和卡車裝置](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-expanded.png#lightbox)

在 [裝置] 頁面中，選取所有的**冷卻器**、**電梯**和**引擎**裝置。 然後，按一下 [作業]  。

在 [作業] 面板中選取 [標記]，將作業名稱設定為 **AddSmartBuildingTag**，然後使用 **SmartBuilding** 值新增名為 **FieldService** 的文字標記。 然後，按一下 [套用]  ：

[![為冷卻器、電梯和引擎裝置新增標記](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-expanded.png#lightbox)

### <a name="create-filters"></a>建立篩選器

現在，您可以使用標記值來建立篩選條件。 在 [裝置總管]  頁面上，按一下 [管理裝置群組]  ：

[![管理裝置群組](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-expanded.png#lightbox)

在條件中建立使用 **FieldService** 標記名稱和 **SmartBuilding** 值的文字篩選條件。 將篩選條件儲存為 [Smart Building]  ：

[![建立智慧建置篩選條件](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-expanded.png#lightbox)

在條件中建立使用 **FieldService** 標記名稱和 **ConnectedVehicle** 值的文字篩選條件。 將篩選條件儲存為 [Connected Vehicle]  。

[![此螢幕擷取畫面顯示已反白顯示 [管理裝置群組] 視窗的 [裝置總管] 頁面。](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-expanded.png#lightbox)

現在，Contoso 操作員能夠根據作業小組來查詢裝置：

[![建立已連線的車輛篩選條件](./media/iot-accelerators-remote-monitoring-manage/filterinaction-inline.png)](./media/iot-accelerators-remote-monitoring-manage/filterinaction-expanded.png#lightbox)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>後續步驟

本教學課程已示範如何設定和管理連線到遠端監視解決方案加速器的裝置。 若要了解如何使用解決方案加速器來執行非預期警示的根本原因分析，請繼續到下一個教學課程。

> [!div class="nextstepaction"]
> [進行警示的根本原因分析](iot-accelerators-remote-monitoring-root-cause-analysis.md)
