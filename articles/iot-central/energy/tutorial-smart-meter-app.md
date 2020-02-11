---
title: 教學課程：使用 IoT Central 建立智慧型電表分析應用程式
description: 教學課程：了解如何使用 Azure IoT Central 應用程式範本來建立智慧型電表監視應用程式。
author: op-ravi
ms.author: omravi
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 9e954e9c1a7efa43a19849b1c5b40284ec84eeed
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77015995"
---
# <a name="tutorial-create-and-walk-through-the-smart-meter-monitoring-app-template"></a>教學課程：建立和逐步執行智慧型電表監視應用程式範本 



本教學課程會引導您完成建立智慧型電表監視應用程式的過程，而且應用程式會包含具有模擬資料的範例裝置模型。 在本教學課程中，您將了解：

> [!div class="checklist"]
> * 免費建立智慧型電表應用程式
> * 應用程式逐步解說
> * 清除資源


如果您沒有訂用帳戶，[請建立免費試用帳戶](https://azure.microsoft.com/free)

## <a name="prerequisites"></a>Prerequisites
- None
- 建議您使用 Azure 訂用帳戶，但您不一定要這麼做

## <a name="create-a-smart-meter-monitoring-app"></a>建立智慧型電表監視應用程式 

您可以用三個簡單的步驟來建立此應用程式：

1. 開啟 [Azure IoT Central 首頁](https://apps.azureiotcentral.com)，然後按一下 [建置]  來建立新的應用程式。 

2. 選取 [能源]  索引標籤，然後按一下 [智慧型電表監視]  應用程式圖格底下的 [建立應用程式]  。

    > [!div class="mx-imgBorder"]
    > ![建置應用程式](media/tutorial-iot-central-smart-meter/smart-meter-build.png)
    

3. [建立應用程式]  將會開啟 [新增應用程式]  表單。 填入要求的詳細資料，如下圖所示：
    * **應用程式名稱**：為您的 IoT Central 應用程式選擇名稱。 
    * **URL**：選擇 IoT Central URL，平台將會驗證其唯一性。
    * **7 天免費試用期**：如果您已經有 Azure 訂用帳戶，建議您使用預設設定。 如果您沒有 Azure 訂用帳戶，請先從免費試用開始。
    * **帳單資訊**：應用程式本身是免費的。 但您需要目錄、Azure 訂用帳戶和區域詳細資料來為您的應用程式佈建資源。
    * 按一下頁面底部的 [建立]  按鈕，您的應用程式將會在一分鐘內建立完成。

        ![新增應用程式表單](media/tutorial-iot-central-smart-meter/smart-meter-create-new-app.png)

        ![新增應用程式表單計費資訊](media/tutorial-iot-central-smart-meter/smart-meter-create-new-app-billinginfo.png)


### <a name="verify-the-application-and-simulated-data"></a>驗證應用程式和模擬的資料

新建立的智慧型電表應用程式就是您的應用程式，您可以隨時進行修改。 在修改之前，讓我們先確定應用程式已部署，並且能如預期般運作。

若要確認應用程式的建立和資料模擬狀況，請移至 [儀表板]  。 如果您可以看到圖格上有一些資料，則表示您的應用程式部署已成功。 資料模擬可能需要幾分鐘的時間來產生資料，請等候 1-2 分鐘。 

## <a name="application-walk-through"></a>應用程式逐步解說
在您成功部署應用程式範本之後，其中會隨附智慧型電表裝置範例、裝置模型和儀表板。 

Adatum 是一家虛構的能源公司，負責監控和管理智慧型電表。 在智慧型電表監視儀表板上，您會看到智慧型電表屬性、資料和命令範例。 這可讓操作員和支援小組在電表變成支援事件前，主動執行下列活動： 
* 檢視最新的電表資訊，以及電表在地圖上的安裝位置
* 主動檢查電表網路和連線狀態 
* 監視網路健康情況的最小和最大電壓數 
* 檢查能源、電力和電壓趨勢，以攔截任何異常模式 
* 追蹤總能源耗用量以用於計劃和計費
* 指揮與控制重新與電表連線和更新韌體版本等作業。 在範本中，命令按鈕會顯示可能的功能，而不會傳送真正的命令。 

> [!div class="mx-imgBorder"]
> ![智慧型電表監視儀表板](media/tutorial-iot-central-smart-meter/smart-meter-dashboard.png)

### <a name="devices"></a>裝置
應用程式會隨附智慧型電表裝置範例。 您可以按一下 [裝置]  索引標籤來查看裝置詳細資料。

> [!div class="mx-imgBorder"]
> ![智慧型電表裝置](media/tutorial-iot-central-smart-meter/smart-meter-devices.png)

按一下範例裝置 **SM0123456789** 連結，以查看裝置的詳細資料。 您可以在 [更新屬性]  頁面上更新裝置的可寫入屬性，並在儀表板上將更新的值視覺化。

> [!div class="mx-imgBorder"]
> ![智慧型計量屬性](media/tutorial-iot-central-smart-meter/smart-meter-device-properties.png)

### <a name="device-template"></a>裝置範本
按一下 [裝置範本]  索引標籤，以查看智慧型電表裝置模型。 模型已針對資料、屬性、命令和檢視預先定義介面。

> [!div class="mx-imgBorder"]
> ![智慧型電表裝置範本](media/tutorial-iot-central-smart-meter/smart-meter-device-template.png)


## <a name="clean-up-resources"></a>清除資源
如果您決定不繼續使用此應用程式，請使用下列步驟來刪除應用程式：

1. 從左側窗格中，開啟 [系統管理] 索引標籤
2. 選取 [應用程式設定]，然後按一下頁面底部的 [刪除] 按鈕。 

    > [!div class="mx-imgBorder"]
    > ![刪除應用程式](media/tutorial-iot-central-smart-meter/smart-meter-delete-app.png)


## <a name="next-steps"></a>後續步驟
* 若要了解智慧型電表應用程式架構，請參考[概念文章](https://docs.microsoft.com/azure/iot-central/energy/concept-iot-central-smart-meter-app)
* 免費建立智慧型電表應用程式範本：[智慧型電表應用程式](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring)
* 若要深入了解 IoT Central，請參閱 [IoT Central 概觀](https://docs.microsoft.com/azure/iot-central/)
