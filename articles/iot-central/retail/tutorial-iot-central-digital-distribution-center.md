---
title: IoT 數位配送中心教學課程 | Microsoft Docs
description: 適用於 IoT Central 的數位配送中心教學課程
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 10/20/2019
ms.openlocfilehash: 59b688803260220866fc331506429a5990c8760a
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2020
ms.locfileid: "97346192"
---
# <a name="tutorial-deploy-and-walk-through-a-digital-distribution-center-application-template"></a>教學課程：部署及逐步執行數位配送中心應用程式範本



此教學課程示範如何透過部署 IoT Central **數位配送中心** 應用程式範本來開始。 您將了解如何部署範本、現成可用的項目，以及您接下來可能想要執行的動作。

在此教學課程中，您將了解如何： 
* 建立數位配送中心應用程式 
* 逐步執行應用程式 

## <a name="prerequisites"></a>Prerequisites
* 部署此應用程式不需要特定的先決條件
* 建議具備 Azure 訂用帳戶，但您甚至不需訂用帳戶就能試用

## <a name="create-digital-distribution-center-application-template"></a>建立數位配送中心應用程式範本

您可以使用下列步驟來建立應用程式

1. 瀏覽至 Azure IoT Central 應用程式管理員網站。 從左側導覽列選取 [建置]  ，然後按一下 [零售]  索引標籤。

    :::image type="content" source="media/tutorial-iot-central-ddc/iotc-retail-homepage.png" alt-text="數位配送中心應用程式範本":::
1. 選取 [零售]  索引標籤，並選取 [數位配送中心應用程式]  下的 [建立應用程式] 

1. [建立應用程式]  將開啟 [新增應用程式] 表單，並填寫要求的詳細資料，如下所示。
   **應用程式名稱**：您可以使用建議的預設名稱，或輸入易記應用程式名稱。
   **URL**：您可以使用建議的預設 URL，或輸入易記且獨特的 URL。 接下來，若您已經有 Azure 訂用帳戶，將建議預設設定。 您可以從 7 天免費試用定價方案開始，並隨時在免費試用到期之前選擇轉換成標準定價方案。
   **帳單資訊**：您需要目錄、Azure 訂用帳戶和區域詳細資料，才能佈建資源。
   **建立**：選取頁面底部的 [建立]，以部署您的應用程式。

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-create.png" alt-text="顯示如何從數位配送中心應用程式範本建立應用程式的螢幕擷取畫面":::

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-create-billinginfo.png" alt-text="顯示當您建立應用程式時計費選項的螢幕擷取畫面":::

## <a name="walk-through-the-application-dashboard"></a>逐步執行應用程式儀表板 

成功部署應用程式範本之後，您的預設儀表板是以配送中心操作員為主的入口網站。 Northwind Trader 是管理輸送機系統的虛構配送中心解決方案提供者。 

在此儀表板中，您會看到一個閘道與一部作為 IoT 裝置的攝影機。 閘道提供有關包裹的遙測 (例如有效、無效、無法辨識和大小)，以及關聯的裝置對應項屬性。 所有下游命令都是在 IoT 裝置 (例如相機) 上執行。 此儀表板已預先設定來展示重要的配送中心裝置操作活動。

儀表板會以邏輯方式組織，以顯示 Azure IoT 閘道與 IoT 裝置的裝置管理功能。  
   * 您可以執行閘道管控工作
   * 管理屬於解決方案一部分的所有攝影機。 

> [!div class="mx-imgBorder"]
> ![顯示數位配送中心儀表板的螢幕擷取畫面](./media/tutorial-iot-central-ddc/ddc-dashboard.png)

## <a name="device-template"></a>裝置範本

按一下 [裝置範本] 索引標籤，您將看到閘道功能模型。 功能模型建構在兩個不同的介面 (**攝影機** 與 **數位配送閘道**) 上

> [!div class="mx-imgBorder"]
> ![顯示應用程式中數位配送閘道裝置範本的螢幕擷取畫面](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)

**相機**：此介面會組織所有相機特定命令功能 

> [!div class="mx-imgBorder"]
> ![顯示數位配送閘道裝置範本中相機介面的螢幕擷取畫面](./media/tutorial-iot-central-ddc/ddc-camera.png)

**數位配送閘道**：此介面代表來自相機、雲端定義的裝置對應項屬性與閘道資訊的所有遙測。

> [!div class="mx-imgBorder"]
> ![顯示數位配送閘道裝置範本中數位配送閘道介面的螢幕擷取畫面](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)


## <a name="gateway-commands"></a>閘道命令
此介面會組織所有閘道命令功能

> [!div class="mx-imgBorder"]
> ![顯示數位配送閘道裝置範本中閘道命令介面的螢幕擷取畫面](./media/tutorial-iot-central-ddc/ddc-camera.png)

## <a name="rules"></a>規則
選取 [規則] 索引標籤，以查看存在於此應用程式範本中的兩個不同規則。 這些規則會設定為向操作員傳送電子郵件通知，以供進一步調查。

 **太多無效包裹警示**：當相機偵測到大量無效包裹通過輸送機時，會觸發此規則。
 
**大型包裹**：若相機偵測到無法檢查品質的大型包裹，會觸發此規則。 

> [!div class="mx-imgBorder"]
> ![顯示數位配送中心應用程式中規則清單的螢幕擷取畫面](./media/tutorial-iot-central-ddc/ddc-rules.png)

## <a name="jobs"></a>工作
選取 [作業] 索引標籤，以查看存在於此應用程式範本中的五個不同作業：您可以利用作業功能來執行整個解決方案的操作。 在這裡，數位配送中心作業會使用裝置命令和對應項功能來執行如下工作：
   * 起始包裹偵測之前校準相機 
   * 定期更新相機韌體
   * 修改遙測間隔以管理資料上傳

> [!div class="mx-imgBorder"]
> ![顯示數位配送中心應用程式中作業清單的螢幕擷取畫面](./media/tutorial-iot-central-ddc/ddc-jobs.png)

## <a name="clean-up-resources"></a>清除資源
如果您不打算繼續使用此應用程式，請覽 [系統管理]   > [應用程式設定]  ，然後按一下 [刪除]  以刪除應用程式範本。

> [!div class="mx-imgBorder"]
> ![顯示當您完成應用程式時如何將其刪除的螢幕擷取畫面](./media/tutorial-iot-central-ddc/ddc-cleanup.png)

## <a name="next-steps"></a>後續步驟
* 深入了解數位配送中心解決方案架構 
> [!div class="nextstepaction"]
> [數位配送中心概念](./architecture-digital-distribution-center.md)
* 深入了解其他 [IoT Central 零售範本](./overview-iot-central-retail.md)
* 若要深入了解 IoT Central，請參閱 [IoT Central 概觀](../core/overview-iot-central.md)
