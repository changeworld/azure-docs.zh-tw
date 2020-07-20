---
title: IoT 聯網物流教學課程 | Microsoft Docs
description: 適用於 IoT Central 的聯網物流應用程式範本教學課程
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: eac43ae68b10436b3e45452c6b1d03bec3ae4c9c
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "81001297"
---
# <a name="tutorial-deploy-and-walk-through-a-connected-logistics-application-template"></a>教學課程：部署並逐步執行聯網物流應用程式範本



此教學課程示範如何開始部署 IoT Central **聯網物流**應用程式範本。 您將了解如何部署範本、現成可用的項目，以及您接下來可能想要執行的動作。

在本教學課程中，您將學會如何：

* 建立聯網物流應用程式
* 逐步執行應用程式 

## <a name="prerequisites"></a>Prerequisites

* 部署此應用程式不需要特定的先決條件
* 建議具備 Azure 訂用帳戶，但您甚至不需訂用帳戶就能試用

## <a name="create-connected-logistics-application-template"></a>建立聯網物流應用程式範本

您可以使用下列步驟來建立應用程式

1. 瀏覽至 Azure IoT Central 應用程式管理員網站。 從左側導覽列選取 [建置]  ，然後按一下 [零售]  索引標籤。

    > [!div class="mx-imgBorder"]
    > ![聯網物流儀表板](./media/tutorial-iot-central-connected-logistics/iotc-retail-homepage.png)

2. 在 [聯網物流應用程式]  底下，選取 [建立應用程式]  。

3. [建立應用程式]  將開啟 [新增應用程式] 表單，並填寫要求的詳細資料，如下所示。
   * **應用程式名稱**：您可以使用建議的預設名稱，或輸入易記應用程式名稱。
   * **URL**：您可以使用建議的預設 URL，或輸入易記且獨特的 URL。 接下來，若您已經有 Azure 訂用帳戶，將建議預設設定。 您可以從 7 天免費試用定價方案開始，並隨時在免費試用到期之前選擇轉換成標準定價方案。
   * **帳單資訊**：您需要目錄、Azure 訂用帳戶和區域詳細資料，才能佈建資源。
   * **建立**：選取頁面底部的 [建立]，以部署您的應用程式。

    > [!div class="mx-imgBorder"]
    > ![聯網物流儀表板](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create.png)

    > [!div class="mx-imgBorder"]
    > ![聯網物流計費資訊](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create-billinginfo.png)

## <a name="walk-through-the-application"></a>逐步執行應用程式 

## <a name="dashboard"></a>儀表板

成功部署應用程式範本之後，您的預設儀表板是以聯網物流操作員為主的入口網站。 Northwind Trader 是一家虛構的物流業者，負責管理海運船隊和陸運車隊。 在此儀表板中，您將看到兩個不同的閘道，其中提供有關出貨的遙測資料，以及您可以執行的相關聯命令、作業和動作。 此儀表板已預先設定來展示重要的物流裝置操作活動。
儀表板會以邏輯方式分成兩個不同的閘道裝置管理操作， 
   * 卡車運送的物流路線和海運的位置詳細資料，為所有多式聯運的重要元素
   * 檢視閘道狀態與相關資訊 

> [!div class="mx-imgBorder"]
> ![聯網物流儀表板](./media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard1.png)

   * 您可以輕鬆地追蹤閘道、作用中和未知標籤的總數。
   * 您可以執行裝置管理操作，例如，更新韌體、停用感應器、啟用感應器、更新感應器閾值、更新遙測間隔，以及更新裝置服務合約。
   * 檢視裝置電池耗用量

> [!div class="mx-imgBorder"]
> ![聯網物流儀表板](./media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard2.png)

## <a name="device-template"></a>裝置範本

按一下 [裝置範本] 索引標籤，您將看到閘道功能模型。 功能模型會以兩個不同的介面 [閘道遙測資料和屬性]  和 [閘道命令]  來結構化

**閘道遙測資料和屬性**：此介面代表所有與感應器、位置和裝置資訊相關的遙測資料，以及裝置對應項屬性功能，例如，感應器閾值和更新間隔。

> [!div class="mx-imgBorder"]
> ![聯網物流儀表板](./media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate1.png)

**閘道命令**：此介面會組織所有閘道命令功能

> [!div class="mx-imgBorder"]
> ![聯網物流儀表板](./media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate2.png)

## <a name="rules"></a>規則
選取 [規則] 索引標籤，以查看存在於此應用程式範本中的兩個不同規則。 這些規則會設定為向操作員傳送電子郵件通知，以供進一步調查。
 
**遭竊警示**：當感應器在路途中意外偵測到光線時，就會觸發此規則。 操作員必須儘快收到通知，才能調查潛在的竊盜行為。
 
**閘道沒有回應**：如果閘道長時間未向雲端回報，此規則將會觸發。 閘道可能因為電池模式電力不足、連線中斷、裝置健康情況而導致沒有回應。

> [!div class="mx-imgBorder"]
> ![聯網物流儀表板](./media/tutorial-iot-central-connected-logistics/connected-logistics-rules.png)

## <a name="jobs"></a>工作
選取 [作業] 索引標籤，以查看存在於此應用程式範本中的五個不同作業：

> [!div class="mx-imgBorder"]
> ![聯網物流儀表板](./media/tutorial-iot-central-connected-logistics/connected-logistics-jobs.png)

您可以使用作業功能來執行整個解決方案的操作。 這裡的作業會使用裝置命令和對應項功能來執行工作，例如，停用所有閘道上的特定感應器，或根據出貨模式和路線修改感應器閾值。 
   * 這是一項標準操作，可在海運期間停用震動感應器以節省電池，或在冷鏈運輸過程中降低溫度閾值。 
 
   * 作業可讓您執行全系統操作，例如，更新閘道上的韌體，或更新服務合約以保持最新的維修活動。

## <a name="clean-up-resources"></a>清除資源
如果您不打算繼續使用此應用程式，請覽 [系統管理]   > [應用程式設定]  ，然後按一下 [刪除]  以刪除應用程式範本。

> [!div class="mx-imgBorder"]
> ![聯網物流儀表板](./media/tutorial-iot-central-connected-logistics/connected-logistics-cleanup.png)

## <a name="next-steps"></a>後續步驟
* 深入了解[聯網物流概念](./architecture-connected-logistics.md)
* 深入了解其他 [IoT Central 零售範本](./overview-iot-central-retail.md)
* 深入了解 [IoT Central 概觀](../core/overview-iot-central.md)
