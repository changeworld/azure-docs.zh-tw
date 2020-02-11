---
title: 教學課程：使用 Azure IoT Central 建立用水量監視應用程式
description: 教學課程：了解如何使用 Azure IoT Central 應用程式範本來建立用水量監視應用程式。
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 1bad4ab6320e757ac766776a95b8dbe6ebaa3259
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77016420"
---
# <a name="tutorial-create-a-water-consumption-monitoring-application-in-iot-central"></a>教學課程：在 IoT Central 中建立用水量監視應用程式



本教學課程會引導您從 IoT Central 用水量監視應用程式範本中，建立 Azure IoT Central 用水量監視應用程式。 

在本教學課程中，您將學會如何： 

> [!div class="checklist"]
> * 使用 Azure IoT Central **用水量監視**範本來建立您的用水量監視應用程式
> * 探索和自訂操作員儀表板 
> * 探索裝置範本
> * 探索模擬裝置
> * 探索和設定規則
> * 設定工作
> * 使用白標自訂您的應用程式商標

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您需要：
-  建議要有 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，則可以在 [Azure 註冊頁面](https://aka.ms/createazuresubscription)上建立一個。

## <a name="create-water-consumption-monitoring-app-in-iot-central"></a>在 IoT Central 中建立用水量監視應用程式

在本節中，您將使用 Azure IoT Central **用水量監視範本**，在 IoT Central 中建立您的用水量監視應用程式。

若要建立新的 Azure IoT Central 用水量監視應用程式：  

1. 瀏覽至 [Azure IoT Central 首頁](https://aka.ms/iotcentral)網站。

    如果您有 Azure 訂用帳戶，請使用您存取時所用的認證來登入，否則請使用 Microsoft 帳戶來登入：

    ![輸入您的組織帳戶](media/tutorial-waterconsumptionmonitoring/sign-in.png)

2. 按一下左側窗格中的 [建置]  ，然後選取 [政府]  索引標籤。[政府] 頁面會顯示數個政府應用程式範本。

   ![建置政府應用程式範本](./media/tutorial-waterconsumptionmonitoring/iotcentral-government-tab-overview1.png)

1. 選取 [用水量監視]  應用程式範本。 此範本包含用水量裝置範本範例、模擬裝置、操作員儀表板，以及預先設定的監視規則。    

2. 按一下 [建立應用程式]  將會開啟 [新增應用程式]  的建立表單，其中包含下列欄位：
    * **應用程式名稱**：根據預設，應用程式會使用 Water consumption monitoring  加上 IoT Central 產生的唯一識別碼字串。 (選擇性) 選擇易記的應用程式名稱。 您也可以在之後變更應用程式名稱。
    * **URL**：IoT Central 會根據應用程式名稱，自動為您產生 URL。 您可以根據自己的喜好來更新 URL。 您也可以在之後變更 URL。 
    * 如果您有 Azure 訂用帳戶，請輸入您的 [目錄]、[Azure 訂用帳戶] 和 [區域]  。 如果您沒有訂用帳戶，則可以啟用 [7 天免費試用]  並填妥必要的連絡人資訊。  

    如需關於目錄和訂用帳戶的詳細資訊，請參閱[建立應用程式快速入門](../core/quick-deploy-iot-central.md)。

5. 按一下頁面底部的 [建立]  按鈕。 

    ![Azure IoT Central 的建立應用程式頁面](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring.png)

    ![Azure IoT Central 的建立應用程式計費資訊](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring-billinginfo.png)

6. 您現在已使用 Azure IoT Central **用水量監視**範本建立了用水量監視應用程式。

恭喜！ 您已完成水質監視應用程式的建立，其中包含預先設定的：
* 範例操作員儀表板
* 預先定義的水資源流程和閥門裝置範本範例
* 模擬的水資源流量和智慧型閥門裝置
* 預先設定的規則和作業
* 使用白標的商標範例 

這是您的應用程式，您可以隨時進行修改。 現在讓我們來探索應用程式，並加入一些自訂內容。  

## <a name="explore-and-customize-operator-dashboard"></a>探索和自訂操作員儀表板 
建立應用程式之後，您會進入稱為 **Wide World 用水量監視儀表板**的操作員儀表板範例。

   ![用水量監視儀表板](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboardfull.png)

身為建置者，您可以在儀表板上為操作員建立及自訂檢視。 在您嘗試進行自訂之前，讓我們來探索一下儀表板。 

> [!NOTE]
> 儀表板中顯示的所有資料都是以模擬裝置資料為基礎，我們將在下一節中加以探索。 
  
儀表板包含不同類型的磚：

* **Wide World Water 公用事業影像磚**：儀表板中的第一個磚是虛構的水資源公用事業 "Wide World Water" 的影像磚。 您可以自訂磚並放入自己的影像，或將其移除。 

* **平均水流量 KPI 磚**：KPI 磚已設定為顯示「過去30分鐘內的平均值」  範例。 您可以自訂 KPI 磚，並設定不同的類型和時間範圍。

* 然後，儀表板的 [裝置命令]  磚有**關閉閥門**、**開啟閥門**或**設定閥門位置**的權限。 按一下命令，系統就會帶您前往模擬裝置的命令頁面。 在 IoT Central 中，「命令」  就是「裝置功能」  的類型，您稍後可以在本教學課程的**裝置範本**一節中進行探索。

*  **水資源分佈區域地圖**：地圖使用的是 Azure 地圖服務，您可以直接在 Azure IoT Central 中進行設定。 地圖底圖顯示的是裝置位置。 嘗試將滑鼠停留在地圖上，並試用地圖上的控制項，例如 [放大]  、[縮小]  或 [展開]  。 

    ![用水量監視儀表板地圖](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboard-map.png)

* **平均水流量折線圖**和**環境條件折線圖**：您可以將一個或多個裝置遙測視覺化，以折線圖呈現所需時間範圍內的資料。  

* **平均閥門壓力熱度圖圖表**：您可以針對想了解的裝置遙測資料，選擇熱度圖的視覺效果類型，以藉由色彩索引來查看一段時間範圍中的分佈情況。

* **重設警示閾值內容磚**：您可以加入內嵌動作頁面連結的召喚動作內容磚。 在此案例中，[重設警示閾值] 會將您帶到應用程式的**作業**，您可以在其中執行裝置屬性的更新，我們稍後會在本教學課程的 [設定作業]  一節中進行探索。

* **屬性磚**：儀表板會顯示**閥門操作資訊**、**流量警示閾值**和**維護資訊**。 


### <a name="customize-dashboard"></a>自訂儀表板 

身為建置者，您可以在儀表板中為操作員自訂檢視。 您可以嘗試：
1. 按一下 [編輯]  ，以自訂 **Wide World 水用量監視儀表板**。 您可以按一下 [編輯]  功能表來自訂儀表板。 當儀表板處於 [編輯]  模式之後，您就可以增加新的磚或進行設定 

     ![編輯儀表板](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-edit-dashboard.png)

2. 按一下 [+ 新增]  以建立新的儀表板，並從頭進行設定。 您可以有多個儀表板，而且可以從 [儀表板] 功能表中瀏覽每個儀表板。 

## <a name="explore-device-template"></a>探索裝置範本
Azure IoT Central 中的裝置範本會定義裝置的功能，例如遙測、屬性或命令。 身為建置者，您可以在 IoT Central 中定義一個或多個裝置範本，以代表您將與之連線的裝置功能。 
 

**用水量監視**應用程式有兩個代表「流量計量」  和「智慧型閥門」  裝置的參考裝置範本。 

若要檢視裝置範本：

1. 在 IoT Central 中您應用程式的左側導覽窗格中，按一下 [裝置範本]  。 
    在 [裝置範本] 清單中，您會看到兩個裝置範本：**流量計量**和**智慧型閥門**

   ![裝置範本](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate.png)

2. 按一下 [流量計量]  裝置範本，並熟悉裝置功能 

     ![裝置範本流量計量](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-flowmeter.png)

### <a name="customizing-the-device-template"></a>自訂裝置範本

嘗試自訂下列項目：
1. 在裝置範本功能表中，瀏覽至 [自訂] 
2. 尋找 `Temperature` 遙測類型
3. 將 `Temperature` 的 [顯示名稱]  更新為 `Reported temperature`。
4. 更新測量單位，或設定 [最小值]  和 [最大值] 
5. **儲存**任何變更 

### <a name="add-a-cloud-property"></a>新增雲端屬性 
1. 在裝置範本功能表中，瀏覽至 [雲端屬性] 
2. 按一下 [+ 新增雲端屬性]  來新增雲端屬性。 
    在 IoT Central 中，您可以新增與裝置相關的屬性。 例如，雲端屬性可能是專屬於安裝區域、資產資訊或其他維護資訊的警示閾值。 
3. **儲存**任何變更 
 
### <a name="views"></a>檢視 
「用水量監視器」裝置範本附有預先定義的檢視。 探索這些檢視，您可以進行更新。 這些檢視會定義操作員如何查看裝置資料，同時也會輸入雲端屬性。 

  ![裝置範本檢視](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-views.png)

### <a name="publish"></a>發佈 
如果您執行了任何變更，請務必**發佈**裝置範本。 

### <a name="create-a-new-device-template"></a>建立新的裝置範本 
- 選取 [+ 新增]  來建立新的裝置範本，並遵循建立程序。 您可以從頭開始建立自訂裝置範本，也可以從 Azure 裝置目錄中選擇裝置範本。 

## <a name="explore-simulated-devices"></a>探索模擬裝置
在 IoT Central 中，您可以建立模擬裝置來測試您的裝置範本和應用程式。 **用水量監視**應用程式有兩個與「流量計量」  和「智慧型閥門」  裝置範本對應的模擬裝置。 

### <a name="to-view-the-devices"></a>若要檢視裝置：
1. 從 IoT Central 的左側導覽窗格中，瀏覽至 [裝置]  。 

   ![裝置](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devices.png)

2. 按一下 [智慧型閥門 1]  

    ![裝置 1](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitor-device1.png)

3.  在 [裝置命令]  中，您可以看到三個裝置命令：開啟閥門  、關閉閥門  及設定閥門位置  ，這些功能都定義在 [智慧型閥門]  裝置範本中。 
4. 依序瀏覽 [裝置屬性]  索引標籤和 [裝置儀表板]  索引標籤。 

> [!NOTE]
> 請注意，所有索引標籤都已從 [裝置範本] 檢視中進行設定。

### <a name="add-new-devices"></a>新增裝置
* 按一下 [裝置]  索引標籤上的 [+ 新增]  ，以新增裝置。 

## <a name="explore-and-configure-rules"></a>探索和設定規則

在 Azure IoT Central 中，您可以建立規則來自動監視裝置遙測，並在符合一個或多個條件時觸發動作。 這些動作可能包括傳送電子郵件通知，或觸發 Microsoft Flow 或 Webhook 動作，以將資料傳送到其他服務。

您從範本中建立的**用水量監視**應用程式有三個預先設定的規則。

### <a name="to-view-rules"></a>若要檢視規則：
1. 從 IoT Central 的左側導覽窗格中，瀏覽至 [規則]  。 

   ![規則](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-rules.png)

2. 選取並按一下 [高 pH 值警示]  ，這是應用程式中其中一個預先設定的規則。

     ![高 pH 值警示](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-highflowalert.png)

    `High flow alert` 規則會設定為檢查 `Acidity (pH)` 條件是否 `greater than``Max flow threshold`。 「最大流量閾值」是「智慧型閥門」  裝置範本中定義的雲端屬性。 `Max flow threshold` 的值是針對每個裝置執行個體所設定。 

現在，我們來建立電子郵件動作。

若要將動作新增至規則：

1. 選取 [+ 電子郵件]  。 
1. 輸入「高 pH 值警示」  作為此動作的自訂 [顯示名稱]  。
    * 在 [收件人]  中，輸入與您 IoT Central 帳戶相關聯的電子郵件地址。 
1. (選擇性) 輸入附註來納入電子郵件的文字。
1. 選取 [完成]  來完成此動作。
1. 選取 [儲存]  以儲存並啟用新的規則。 

幾分鐘內，您應該會在設定**條件**相符時收到電子郵件。

> [!NOTE]
> 應用程式會在每次條件相符時傳送電子郵件。 **停用**規則可停止從自動化規則接收電子郵件。 
  
若要建立新的規則： 
- 在左側導覽窗格的 [規則]  中，選取 [+新增]  。

## <a name="configure-jobs"></a>設定作業

在 IoT Central 中，作業可讓您在多個裝置上觸發裝置或雲端屬性更新。 除了屬性之外，您也可以使用作業來觸發多個裝置上的裝置命令。 IoT Central 會自動為您執行工作流程。 

1. 從左側導覽窗格中移至 [作業]  。 
2. 按一下 [+新增]  並設定一個或多個作業。 


## <a name="customize-your-application"></a>自訂應用程式 
身為建置者，您可以變更數個設定，以自訂應用程式的使用者體驗。

1.  移至 [系統管理] > [自訂應用程式]  。
3. 使用 [變更]  按鈕來選擇要上傳作為**應用程式標誌**的影像。
4. 使用 [變更]  按鈕，以選擇將會出現在瀏覽器索引標籤上的**瀏覽器圖示**影像。
5. 您也可以藉由新增 HTML 十六進位色彩代碼來取代預設的 [瀏覽器色彩]  。

   ![Azure IoT Central 自訂應用程式](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-customize-your-application.png)

1.  若要變更應用程式影像，您也可以移至 [系統管理] > [應用程式設定]  ，然後按一下 [選取影像]  按鈕來選擇要上傳為應用程式影像的影像。 
2. 最後，您也可以在應用程式的標題上按一下 [設定]  ，以變更**主題**。 

  
## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續使用此應用程式，請使用下列步驟刪除您的應用程式：

1. 從 IoT Central 應用程式的左側窗格中，開啟 [系統管理] 索引標籤。 
2. 選取 [應用程式設定]，然後按一下頁面底部的 [刪除] 按鈕。 


## <a name="next-steps"></a>後續步驟

* 深入了解[用水量監視概念](./concepts-waterconsumptionmonitoring-architecture.md)
