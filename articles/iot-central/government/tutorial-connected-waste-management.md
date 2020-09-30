---
title: 教學課程：使用 Azure IoT Central 建立聯網廢棄物管理應用程式
description: 教學課程：了解使用 Azure IoT Central 應用程式範本建立聯網廢棄物管理應用程式。
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 53304f14286577bf0bc25b2a66442286527c5140
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90972433"
---
# <a name="tutorial-create-a-connected-waste-management-application-in-iot-central"></a>教學課程：在 IoT Central 中建立聯網廢棄物管理應用程式



本教學課程會引導您從 IoT Central **聯網廢棄物管理**應用程式範本，建立 Azure IoT Central 聯網廢棄物管理應用程式。 

在本教學課程中，您將學會如何： 

> [!div class="checklist"]

> * 使用 Azure IoT Central **聯網廢棄物管理**範本來建立聯網廢棄物管理應用程式
> * 探索和自訂操作員儀表板 
> * 探索聯網垃圾桶裝置範本
> * 探索模擬裝置
> * 探索和設定規則
> * 設定工作
> * 使用白標自訂您的應用程式商標

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您需要：
-  建議要有 Azure 訂用帳戶。 您可以選擇使用免費的 7 天試用版。 如果您沒有 Azure 訂用帳戶，則可以在 [Azure 註冊頁面](https://aka.ms/createazuresubscription)上建立一個。

## <a name="create-connected-waste-management-app-in-iot-central"></a>在 IoT Central 中建立聯網廢棄物管理應用程式

在本節中，您會使用 Azure IoT Central **聯網廢棄物管理**範本，在 IoT Central 中建立聯網廢棄物管理管理應用程式。

若要建立新的 Azure IoT Central 聯網廢棄物管理應用程式：  

1. 瀏覽至 [Azure IoT Central 首頁](https://aka.ms/iotcentral)網站。

    如果您有 Azure 訂用帳戶，請使用您存取時所用的認證來登入，否則請使用 Microsoft 帳戶來登入：

    ![輸入您的組織帳戶](./media/tutorial-connectedwastemanagement/sign-in.png)

2. 按一下左側窗格中的 [建置]  ，然後選取 [政府]  索引標籤。[政府] 頁面會顯示數個政府應用程式範本。

    ![建置政府應用程式範本](./media/tutorial-connectedwastemanagement/iotcentral-government-tab-overview.png)

1. 選取**聯網廢棄物管理**應用程式範本。 此範本包含範例聯網垃圾桶裝置範本、模擬裝置、操作員儀表板，以及預先設定的監視規則。    

2. 按一下 [建立應用程式]  將會開啟 [新增應用程式]  的建立表單，其中包含下列欄位：
    * **應用程式名稱**： 根據預設，應用程式會使用*聯網廢棄物管理*，後面接著 IoT Central 產生的唯一識別碼字串。 (選擇性) 選擇易記的應用程式名稱。 您也可以在之後變更應用程式名稱。
    * **URL** - 您可以選擇性地選擇想要的 URL。 您也可以在之後變更 URL。 
    * 如果您有 Azure 訂用帳戶，請輸入您的 [目錄]、[Azure 訂用帳戶] 和 [區域]  。 如果您沒有訂用帳戶，則可以啟用 [7 天免費試用]  並填妥必要的連絡人資訊。  

    如需關於目錄和訂用帳戶的詳細資訊，請參閱[建立應用程式快速入門](../core/quick-deploy-iot-central.md)。

5. 按一下頁面底部的 [建立]  按鈕。 

    ![Azure IoT Central 建立聯網廢棄物應用程式頁面](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement.png)
    
    ![Azure IoT Central 建立聯網計費資訊](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement-billinginfo.png)

 
6. 現在您已使用 Azure IoT Central **聯網廢棄物管理範本**建立聯網廢棄物管理應用程式。 

恭喜！ 新建立的應用程式會隨附以下預先設定項目：
* 範例操作員儀表板
* 範例預先設定聯網垃圾桶裝置範本
* 模擬連線垃圾箱裝置
* 預先設定的規則和作業
* 使用白標的商標範例 

這是您的應用程式，您可以隨時進行修改。 現在讓我們來探索應用程式，並加入一些自訂內容。  

## <a name="explore-and-customize-operator-dashboard"></a>探索和自訂操作員儀表板 
建立應用程式之後，您會進入 **Wide World Waste 聯網廢棄物管理儀表板**。

   ![聯網廢棄物管理儀表板](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard1.png)

身為建置者，您可以在儀表板上為操作員建立及自訂檢視。 在您嘗試進行自訂之前，讓我們來探索一下儀表板。 

> [!NOTE]
> 儀表板中顯示的所有資料都是以模擬裝置資料為基礎，我們將在下一節中加以探索。 

儀表板包含不同類型的磚：

* ***Wide World Waste 公用事業影像磚***：儀表板的第一個磚是虛構廢棄物公用事業「Wide World Waste」的影像磚。 您可以自訂磚並放入自己的影像，或將其移除。 

* ***垃圾桶影像磚***：您可以使用影像和內容磚，來建立要與描述性文字一起進行監控的裝置視覺表示。 

* ***填滿層級 KPI 磚***：此磚顯示由垃圾桶中「填滿層級」  感應器所報告的值。 垃圾桶中的「填滿層級」  和其他感應器 (例如「氣味計量」  或「重量」  ) 可以遠端進行監控。 操作員可以採取動作，例如分派垃圾車。 

*  ***廢棄物監控區域地圖***：地圖使用的是 Azure 地圖服務，您可以直接在 Azure IoT Central 中進行設定。 地圖底圖顯示的是裝置位置。 嘗試將滑鼠停留在地圖上，並試用地圖上的控制項，例如 [放大]、[縮小] 或 [展開]。

     ![聯網廢棄物管理儀表板地圖](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-map.png)


* ***填滿、氣味、重量層級橫條圖**：您可以在橫條圖中將一或多個裝置遙測資料視覺化。 您也可以展開橫條圖。  

  ![聯網廢棄物管理儀表板橫條圖](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-barchart.png)


* **現場服務內容磚**：儀表板包含如何從 Azure IoT Central 應用程式整合 Dynamics 365 現場服務的連結。 例如，您可以使用現場服務來建立用於分派垃圾收集服務的票證。 


### <a name="customize-dashboard"></a>自訂儀表板 

身為建置者，您可以在儀表板中為操作員自訂檢視。 您可以嘗試：
1. 按一下 [編輯]  以自訂 **Wide World 聯網廢棄物管理儀表板**。 您可以按一下 [編輯]  功能表來自訂儀表板。 當儀表板處於 [編輯]  模式之後，您就可以增加新的磚或進行設定 

    ![編輯儀表板](./media/tutorial-connectedwastemanagement/edit-dashboard.png)

2. 您也可以按一下 [+ 新增]  以建立新的儀表板，並從頭進行設定。 您可以有多個儀表板，而且可以從 [儀表板] 功能表中瀏覽每個儀表板。 

## <a name="explore-connected-waste-bin-device-template"></a>探索聯網垃圾桶裝置範本

Azure IoT Central 中的裝置範本會定義裝置的功能，例如遙測、屬性或命令。 身為建置者，您可以定義裝置範本，以代表您將與之連線的裝置功能。 
 

**聯網廢棄物管理**應用程式隨附一個範例聯網垃圾桶裝置範本。

若要檢視裝置範本：

1. 在 IoT Central 中您應用程式的左窗格中，按一下 [裝置範本]  。 

    ![顯示應用程式中裝置範本清單的螢幕擷取畫面](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate.png)

2. 在裝置範本清單中，您會看到 [聯網垃圾桶]  。 按一下名稱來開啟。

3. 熟悉裝置範本功能。 您可以看到其定義感應器，例如「填滿層級」  、「氣味計量」  、「重量」  、「位置」  等等。

   ![顯示已聯網垃圾桶裝置範本詳細資料的螢幕擷取畫面](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-connectedbin.png)

   

### <a name="customizing-the-device-template"></a>自訂裝置範本

嘗試自訂下列項目：
1. 在裝置範本功能表中，瀏覽至 [自訂] 
2. 尋找 `Odor meter` 遙測類型
3. 將 `Odor meter` 的 [顯示名稱]  更新為 `Odor level`。
4. 您也可以嘗試更新測量單位，或設定 [最小值]  和 [最大值] 
5. **儲存**任何變更 

### <a name="add-a-cloud-property"></a>新增雲端屬性 

1. 在裝置範本功能表中，瀏覽至 [雲端屬性] 
2. 按一下 [+ 新增雲端屬性]  來新增雲端屬性。 在 IoT Central 中，您可以新增與裝置相關但不會由裝置傳送的屬性。 例如，雲端屬性可能是專屬於安裝區域、資產資訊或維護資訊等資訊的警示閾值。 
3. **儲存**任何變更 
 
### <a name="views"></a>檢視 
* 聯網垃圾桶裝置範本隨附預先定義的檢視。 探索這些檢視，您可以進行更新。 這些檢視會定義操作員如何查看裝置資料，同時也會輸入雲端屬性。 

  ![裝置範本檢視](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-views.png)

### <a name="publish"></a>發佈 

* 如果您執行了任何變更，請務必**發佈**裝置範本。 

### <a name="create-a-new-device-template"></a>建立新的裝置範本 

* 選取 [+ 新增]  來建立新的裝置範本，並遵循建立程序。 您可以從頭開始建立自訂裝置範本，也可以從 Azure 裝置目錄中選擇裝置範本。 

## <a name="explore-simulated-devices"></a>探索模擬裝置

在 IoT Central 中，您可以建立模擬裝置來測試您的裝置範本和應用程式。 

**聯網廢棄物管理**應用程式有兩個模擬裝置，對應至聯網垃圾桶裝置範本。 

### <a name="to-view-the-devices"></a>若要檢視裝置：

1. 從 IoT Central 的左窗格瀏覽至 [裝置]  。 

   ![裝置](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices.png)

2. 選取並按一下 [聯網垃圾桶裝置]。  

     ![裝置 1](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices-bin1.png)

3. 瀏覽至 [雲端屬性]  索引標籤，嘗試將 `Bin full alert threshold` 值從 `95` 更新為 `100`。 
* 依序瀏覽 [裝置屬性]  索引標籤和 [裝置儀表板]  索引標籤。 

> [!NOTE]
> 請注意，所有索引標籤都已從 [裝置範本檢視]  中進行設定。

### <a name="add-new-devices"></a>新增裝置

* 您可以按一下 [裝置]  索引標籤上的 [+ 新增]  ，以新增裝置。 

## <a name="explore-and-configure-rules"></a>探索和設定規則

在 Azure IoT Central 中，您可以建立規則來自動監視裝置遙測，並在符合一個或多個條件時觸發動作。 這些動作可能包括傳送電子郵件通知，觸發 Microsoft Flow 或 Webhook 動作，以將資料傳送到其他服務。

**聯網廢棄物管理**應用程式有四個範例規則。

### <a name="to-view-rules"></a>若要檢視規則：
1. 從 IoT Central 的左窗格瀏覽至 [規則]  。

   ![規則](./media/tutorial-connectedwastemanagement/connectedwastemanagement-rules.png)

2. 選取 [垃圾桶已滿警示] 

     ![垃圾桶已滿警示](./media/tutorial-connectedwastemanagement/connectedwastemanagement-binfullalert.png)

 3. `Bin full alert` 會檢查**條件** `Fill level is greater than or equal to Bin full alert threshold`。

    `Bin full alert threshold` 是在`Connected waste bin`裝置範本中定義的「雲端屬性」  。 

現在，我們來建立電子郵件動作。

### <a name="create-an-email-action"></a>建立電子郵件動作
若要在 [規則] 動作清單中設定電子郵件動作：
1. 選取 [+ 電子郵件]  。 
2. 輸入「高 pH 值警示」  作為此動作的自訂 [顯示名稱]  。
3. 在 [收件人]  中，輸入與您 IoT Central 帳戶相關聯的電子郵件地址。 
4. (選擇性) 輸入附註來納入電子郵件的文字。
5. 選取 [完成]  來完成此動作。
6. 選取 [儲存]  以儲存並啟用新的規則。 

您應該會在設定**條件**相符時收到電子郵件。

> [!NOTE]
> 應用程式會在每次條件相符時傳送電子郵件。 **停用**規則可停止從自動化規則接收電子郵件。 
  
若要建立新的規則： 
1. 在左窗格的 [規則]  上，選取 [+ 新增]  。

## <a name="configure-jobs"></a>設定作業

在 IoT Central 中，作業可讓您在多個裝置上觸發裝置或雲端屬性更新。 除了屬性之外，您也可以使用作業來觸發多個裝置上的裝置命令。 IoT Central 會自動為您執行工作流程。 

1. 從左窗格移至 [作業]  。 
2. 按一下 [+新增]  並設定一個或多個作業。 


## <a name="customize-your-application"></a>自訂應用程式 

身為建置者，您可以變更數個設定，以自訂應用程式的使用者體驗。

### <a name="to-change-the-application-theme"></a>若要變更應用程式主題：

1. 移至 [系統管理] > [自訂應用程式]  。
3. 使用 [變更]  按鈕來選擇要上傳作為**應用程式標誌**的影像。
4. 使用 [變更]  按鈕，以選擇將會出現在瀏覽器索引標籤上的**瀏覽器圖示**影像。
5. 您也可以藉由新增 HTML 十六進位色彩代碼來取代預設的 [瀏覽器色彩]  。

   ![Azure IoT Central 自訂應用程式](./media/tutorial-connectedwastemanagement/connectedwastemanagement-customize-your-application.png)

6. 若要變更應用程式影像，您也可以移至 [系統管理] > [應用程式設定]  ，然後按一下 [選取影像]  按鈕來選擇要上傳為應用程式影像的影像。
7. 最後，您也可以在應用程式的標題上按一下 [設定]  ，以變更**主題**。

  
## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續使用此應用程式，請使用下列步驟刪除您的應用程式：

1. 從 IoT Central 應用程式的左側窗格中，開啟 [系統管理] 索引標籤。
2. 選取 [應用程式設定]，然後按一下頁面底部的 [刪除] 按鈕。

## <a name="next-steps"></a>後續步驟

* 深入了解
 
> [!div class="nextstepaction"]
> [聯網廢棄物管理概念](./concepts-connectedwastemanagement-architecture.md)
