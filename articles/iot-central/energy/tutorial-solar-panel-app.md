---
title: 教學課程：使用 Azure IoT Central 建立太陽能板監視應用程式
description: 教學課程：了解如何使用 Azure IoT Central 應用程式範本建立太陽能板應用程式。
author: op-ravi
ms.author: omravi
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 9ea1db982a6944bd12b458624545b3888881508f
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97881912"
---
# <a name="tutorial-create-and-explore-the-solar-panel-monitoring-app-template"></a>教學課程：建立及探索太陽能板監視應用程式範本 

本教學課程會引導您完成建立太陽能板監視應用程式的程序，且應用程式會包含具有模擬資料的範例裝置模型。 在本教學課程中，您將了解如何：


> [!div class="checklist"]
> * 免費建立太陽能板應用程式
> * 逐步執行應用程式
> * 清除資源


如果您沒有訂用帳戶，[請建立免費試用帳戶](https://azure.microsoft.com/free)。

## <a name="prerequisites"></a>必要條件

您無需具備先決條件就能完成本教學課程。 建議您使用 Azure 訂用帳戶，但不一定要這麼做。


## <a name="create-a-solar-panel-monitoring-app"></a>建立太陽能板監視應用程式 

您可以用三個簡單的步驟來建立此應用程式：

1. 前往 [Azure IoT Central](https://apps.azureiotcentral.com)。 若要建立新應用程式，請選取 [建置]。 

1. 選取 **能源** 索引標籤。在 **太陽能板監視** 底下，選取 [建立應用程式]。 

    > [!div class="mx-imgBorder"]
    > ![Azure IoT Central 建置選項的螢幕擷取畫面。](media/tutorial-iot-central-solar-panel/solar-panel-build.png)
  
1. 在 **新增應用程式** 對話方塊中，填入要求的詳細資料，然後選取 [建立]：
    * **應用程式名稱**：為您的 Azure IoT Central 應用程式選擇名稱。 
    * **URL**：挑選 Azure IoT Central URL。 平台會驗證其唯一性。
    * **定價方案**：如果您已經有 Azure 訂用帳戶，建議您使用預設設定。 如果您沒有 Azure 訂用帳戶，請先從免費試用開始。
    * **帳單資訊**：應用程式本身是免費的。 但您需要目錄、Azure 訂用帳戶和區域詳細資料來為您的應用程式佈建資源。
        ![新應用程式的螢幕擷取畫面。](media/tutorial-iot-central-solar-panel/solar-panel-create-app.png)
        
        ![帳單資訊的螢幕擷取畫面。](media/tutorial-iot-central-solar-panel/solar-panel-create-app-billinginfo.png)


### <a name="verify-the-application-and-simulated-data"></a>驗證應用程式和模擬的資料

您可以隨時修改新的太陽能板應用程式。 現在，讓我們先確定應用程式已部署，並且能如預期般運作。

若要確認應用程式的建立和資料模擬狀況，請移至 [儀表板]。 如果您可以看到圖格上有一些資料，則表示您的應用程式部署已成功。 資料模擬可能需要幾分鐘來產生資料。 

## <a name="application-walk-through"></a>應用程式逐步解說
成功部署應用程式範本之後，您會想要更深入探索應用程式。 請注意，應用程式隨附範例智慧型計量裝置、裝置型號和儀表板。

Adatum 是一家虛構的能源公司，負責監視和管理太陽能板。 在太陽能板監視儀表板上，您會看到太陽能板屬性、資料和命令範例。 此儀表板可讓您或您的支援小組在發生任何問題需要額外支援之前，主動執行下列活動：
* 檢視最新的太陽能板資訊，以及太陽能板在地圖上的安裝位置。
* 檢查太陽能板狀態和連線狀態。
* 檢查能源產生和溫度趨勢，以攔截任何異常模式。
* 追蹤總能源產生以供規劃和計費之用。
* 視需要啟動面板並更新韌體版本。 在範本中，命令按鈕會顯示可能的功能，而不會傳送真正的命令。

> [!div class="mx-imgBorder"]
> ![太陽能板監視範本儀表板的螢幕擷取畫面。](media/tutorial-iot-central-solar-panel/solar-panel-dashboard.png)

### <a name="devices"></a>裝置
應用程式會隨附範例太陽能板裝置。 要查看裝置詳細資料，請選取 [裝置]。

> [!div class="mx-imgBorder"]
> ![太陽能板監視範本裝置的螢幕擷取畫面。](media/tutorial-iot-central-solar-panel/solar-panel-device.png)

選取範例裝置，**SP0123456789**。 在 **更新屬性** 索引標籤上，您可以更新裝置的可寫入屬性，並在儀表板上看到更新的值以視覺方式顯示。 

> [!div class="mx-imgBorder"]
> ![太陽能板監視範本更新屬性索引標籤的螢幕擷取畫面。](media/tutorial-iot-central-solar-panel/solar-panel-device-properties.png)


### <a name="device-template"></a>裝置範本
選取 [裝置範本] 索引標籤，以查看太陽能板裝置模型。模型已針對資料、屬性、命令和檢視預先定義介面。

> [!div class="mx-imgBorder"]
> ![太陽能板監視範本裝置範本的螢幕擷取畫面。](media/tutorial-iot-central-solar-panel/solar-panel-device-templates.png)


## <a name="clean-up-resources"></a>清除資源
如果您決定不繼續使用此應用程式，請使用下列步驟來刪除應用程式：

1. 從左側窗格，選取 [管理]。
1. 選取 [應用程式設定] > [刪除]。 

    > [!div class="mx-imgBorder"]
    > ![太陽能板監視範本管理的螢幕擷取畫面。](media/tutorial-iot-central-solar-panel/solar-panel-delete-app.png)

## <a name="next-steps"></a>後續步驟
 
> [!div class="nextstepaction"]
> [Azure IoT Central - 太陽能板應用程式架構](./concept-iot-central-solar-panel-app.md)
* [免費建立太陽能板應用程式範本太陽能板應用程式](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring)
* [Azure IoT Central 概觀](../index.yml)
