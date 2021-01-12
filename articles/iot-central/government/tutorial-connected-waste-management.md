---
title: 教學課程：使用 Azure IoT Central 建立聯網廢棄物管理應用程式
description: 了解使用 Azure IoT Central 應用程式範本建置聯網廢棄物管理應用程式。
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 8085409c3dc38d17b6fe0d3cb15857b2396e23b6
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97881339"
---
# <a name="tutorial-create-a-connected-waste-management-app"></a>教學課程：建立連線廢棄物管理應用程式

本教學課程說明如何使用 Azure IoT Central 來建立聯網廢棄物管理應用程式。 

具體而言，您將了解如何： 

* 使用 Azure IoT Central「聯網廢棄物管理」範本來建立您的應用程式。
* 探索和自訂操作員儀表板。 
* 探索聯網垃圾桶裝置範本。
* 探索模擬裝置。
* 探索和設定規則。
* 設定作業。
* 自訂您的應用程式商標。

## <a name="prerequisites"></a>Prerequisites

建議要有 Azure 訂用帳戶。 您可以改用免費的 7 天試用版。 如果您沒有 Azure 訂用帳戶，則可以在 [Azure 註冊頁面](https://aka.ms/createazuresubscription)上建立一個。

## <a name="create-your-app-in-azure-iot-central"></a>在 Azure IoT Central 建立您的應用程式

在本節中，您會使用聯網廢棄物管理範本，在 Azure IoT Central 中建立您的應用程式。 其做法如下：

1. 前往 [Azure IoT Central](https://aka.ms/iotcentral)。

    如果您有 Azure 訂用帳戶，請使用您存取時所用的認證來登入。 否則，請使用 Microsoft 帳戶來登入：

    ![Microsoft 登入的螢幕擷取畫面。](./media/tutorial-connectedwastemanagement/sign-in.png)

1. 從左側窗格中，選取 [建置]。 然後選取 [Government] 索引標籤。[政府] 頁面會顯示數個政府應用程式範本。

    ![Azure IoT Central 組建頁面的螢幕擷取畫面。](./media/tutorial-connectedwastemanagement/iotcentral-government-tab-overview.png)

1. 選取 **聯網廢棄物管理** 應用程式範本。 此範本包含聯網廢棄物垃圾桶裝置範本、模擬裝置、操作員儀表板，以及預先設定的監視規則。    

1. 選取 [建立應用程式]，這會開啟 **新增應用程式** 對話方塊。 在下列欄位中填入資訊：
    * **應用程式名稱**： 根據預設，應用程式會使用 **聯網廢棄物管理**，後面接著 Azure IoT Central 產生的唯一識別碼字串。 您也可以選擇易記的應用程式名稱。 您也可以在之後變更應用程式名稱。
    * **URL**。 您也可以選擇性地選擇想要的 URL。 您也可以在之後變更 URL。 
    * **定價方案**。 如果您有 Azure 訂用帳戶，請在 **帳單資訊** 對話方塊的適當欄位中輸入目錄、Azure 訂用帳戶和區域。 如果沒有訂用帳戶，可以選取 [免費] 以啟用 7 天免費試用訂用帳戶，並填妥必要的連絡人資訊。  

    如需關於目錄和訂用帳戶的詳細資訊，請參閱[快速入門 - 建立 Azure IoT Central 應用程式](../core/quick-deploy-iot-central.md)。

1. 選取頁面底部的 [建立]。 

    ![Azure IoT Central 的新增應用程式對話方塊螢幕擷取畫面。](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement.png)
    
    ![Azure IoT Central 帳單資訊對話方塊螢幕擷取畫面。](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement-billinginfo.png)

 
新建立的應用程式會隨附以下預先設定項目：
* 範例操作員儀表板。
* 範例預先設定聯網垃圾桶裝置範本。
* 模擬連線垃圾箱裝置。
* 規則及作業。
* 範例商標。 

這是您的應用程式，您可以隨時進行修改。 現在讓我們來探索應用程式，並加入一些自訂內容。  

## <a name="explore-and-customize-the-operator-dashboard"></a>探索和自訂操作員儀表板 

查看 **Wide World 廢棄物管理儀表板**，您會在建立應用程式之後看到此儀表板。

   ![Wide World 廢棄物管理儀表板螢幕擷取畫面。](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard1.png)

身為建置者，您可以在儀表板上為操作員建立及自訂檢視。 首先，讓我們來探索儀表板。 

>[!NOTE]
>儀表板中顯示的所有資料都是以模擬裝置資料為基礎，您會在下一節中看到更多內容。 

儀表板包含不同的底圖：

* **Wide World 廢棄物公司影像底圖**：儀表板中的第一個底圖是虛構的 Wide World 廢棄物公司影像圖格。 您可以自訂底圖並放入自己的影像，或移除底圖。 

* **廢棄物垃圾桶影像底圖**：您可以使用影像和內容底圖，來建立要與描述一起進行監控的裝置視覺表示。 

* **滿桶等級 KPI 底圖**：此底圖顯示由垃圾桶中「滿桶等級」感應器所報告的值。 垃圾桶中的滿桶等級和其他感應器 (例如「氣味計量」或「重量」) 可以遠端進行監控。 操作員可以採取動作，例如分派垃圾車。 

* **廢棄物監視區域地圖**：此底圖使用的是 Azure 地圖服務，您可以直接在 Azure IoT Central 中進行設定。 地圖底圖會顯示裝置位置。 嘗試將滑鼠停留在地圖上，並試用地圖上的控制項，例如放大、縮小或展開。

     ![聯網垃圾管理範本儀表板地圖的螢幕擷取畫面。](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-map.png)


* **滿桶、氣味、重量等級橫條圖**：您可以在橫條圖中將一或多個類型的裝置遙測資料視覺化。 您也可以展開橫條圖。  

  ![聯網廢棄物管理範本儀表板橫條圖的螢幕擷取畫面。](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-barchart.png)


* **現場服務**：儀表板包含如何從 Azure IoT Central 應用程式整合 Dynamics 365 現場服務的連結。 例如，您可以使用現場服務來建立用於分派垃圾收集服務的票證。 


### <a name="customize-the-dashboard"></a>自訂儀表板 

您可以選取 [編輯]  功能表來自訂儀表板。 然後您可以加入新的地圖底圖或設定現有的圖格。 以下是儀表板在編輯模式中看起來的樣子： 

![聯網廢棄物管理範本儀表板處於編輯模式的螢幕擷取畫面。](./media/tutorial-connectedwastemanagement/edit-dashboard.png)

您也可以選取 [+ 新增] 以建立新的儀表板，並從頭進行設定。 您可以有多個儀表板，而且可以從 [儀表板] 功能表中切換每個儀表板。 

## <a name="explore-the-device-template"></a>探索裝置範本

Azure IoT Central 中的裝置範本會定義裝置的功能，包括遙測、屬性或命令。 身為建置者，您可以定義裝置範本，以代表您將與之連線的裝置功能。 

聯網廢棄物管理應用程式隨附一個聯網垃圾桶裝置範例範本。

若要檢視裝置範本：

1. 在 Azure IoT Central 中應用程式的左側窗格上，選取 [裝置範本]。 

    ![顯示應用程式中裝置範本清單的螢幕擷取畫面。](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate.png)

1. 在 **裝置範本** 清單中，選取 [聯網垃圾桶]。

1. 檢查裝置範本功能。 您可以看到範本會定義感應器，例如「滿桶等級」、「氣味計量」、「重量」和「位置」等等。

   ![顯示聯網垃圾桶裝置範本詳細資料的螢幕擷取畫面。](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-connectedbin.png)


### <a name="customize-the-device-template"></a>自訂裝置範本

嘗試自訂下列項目：
1. 在裝置範本功能表中，選取 [自訂]  。
1. 尋找 **氣味計量** 遙測類型。
1. 將 **氣味計量** 的 **顯示名稱** 更新為 **氣味等級**。
1. 試著更新測量單位，或設定 [最小值] 和 [最大值]。
1. 選取 [儲存]  。 

### <a name="add-a-cloud-property"></a>新增雲端屬性 

其做法如下：
1. 在裝置範本功能表中，選取 [雲端屬性]  。
1. 選取 [+ 新增雲端屬性]。 在 Azure IoT Central 中，您可以新增與裝置相關但不會由裝置傳送的屬性。 例如，雲端屬性可能是專屬於安裝區域、資產資訊或維護資訊項目的警示閾值。 
1. 選取 [儲存]。 
 
### <a name="views"></a>檢視 
聯網垃圾桶裝置範本隨附預先定義的檢視。 探索檢視，並視需要更新。 這些檢視會定義操作員如何查看裝置資料和輸入雲端屬性。 

  ![聯網廢棄物管理範本裝置範本檢視的螢幕擷取畫面。](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-views.png)

### <a name="publish"></a>發佈 

如果您執行了任何變更，請記得發佈裝置範本。 

### <a name="create-a-new-device-template"></a>建立新的裝置範本 

選取 [+ 新增] 來建立新的裝置範本，並遵循以下步驟。 您可以從頭開始建立自訂裝置範本，也可以從 Azure 裝置目錄中選擇裝置範本。 

## <a name="explore-simulated-devices"></a>探索模擬裝置

在 Azure IoT Central 中，您可以建立模擬裝置來測試裝置範本和應用程式。 

聯網廢棄物管理應用程式有兩個模擬裝置，與聯網垃圾桶裝置範本相關聯。 

### <a name="view-the-devices"></a>檢視裝置

1. 從 Azure IoT Central 的左側窗格中，選取 [裝置]。 

   ![聯網廢棄物管理範本裝置的螢幕擷取畫面。](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices.png)

1. 選取 [聯網廢棄物垃圾桶] 裝置。  

     ![聯網廢棄物管理範本裝置屬性的螢幕擷取畫面。](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices-bin1.png)

1. 移至 **雲端屬性** 索引標籤。將 **垃圾桶已滿警示閾值** 的值從 **95** 更新為 **100**。 

依序瀏覽 [裝置屬性] 和 [裝置儀表板] 索引標籤。 

> [!NOTE]
> 所有索引標籤都已從裝置範本檢視中設定。

### <a name="add-new-devices"></a>新增裝置

您可以選取 [裝置] 索引標籤上的 [+ 新增]，以新增裝置。 

## <a name="explore-and-configure-rules"></a>探索和設定規則

在 Azure IoT Central 中，您可以建立規則來自動監視裝置遙測，並在符合一個或多個條件時觸發動作。 這些動作可能包括傳送電子郵件通知、觸發 Power Automate 動作或啟動 Webhook 動作，以將資料傳送到其他服務。

聯網廢棄物管理應用程式有四個範例規則。

### <a name="view-rules"></a>檢視規則
1. 從 Azure IoT Central 的左側窗格中，選取 [規則]。

   ![聯網廢棄物管理範本規則的螢幕擷取畫面。](./media/tutorial-connectedwastemanagement/connectedwastemanagement-rules.png)

1. 選取 [垃圾桶已滿警示]。

     ![垃圾桶已滿警示的螢幕擷取畫面。](./media/tutorial-connectedwastemanagement/connectedwastemanagement-binfullalert.png)

 1. **垃圾桶已滿警示** 會檢查下列條件：**滿桶等級大於或等於垃圾桶已滿警示閾值**。

    **垃圾桶已滿警示閾值** 是聯網廢棄物垃圾桶裝置範本中定義的雲端屬性。 

現在，我們來建立電子郵件動作。

### <a name="create-an-email-action"></a>建立電子郵件動作

在規則的 **動作** 清單中設定電子郵件動作：
1. 選取 [+ 電子郵件]  。 
1. 在 **顯示名稱** 方塊中，輸入 **高 pH 值警示**。
1. 在 **收件人** 中，輸入與您 Azure IoT Central 帳戶相關聯的電子郵件地址。 
1. (選擇性) 輸入附註來納入電子郵件的文字。
1. 選取 [完成] > [儲存]。 

您會在設定條件相符時收到電子郵件。

>[!NOTE]
>應用程式會在每次條件相符時傳送電子郵件。 停用規則可停止從自動化規則接收電子郵件。 
  
若要建立新的規則，請從 **規則** 的左側窗格中，選取 [+新增]。

## <a name="configure-jobs"></a>設定工作

在 Azure IoT Central 中，作業可讓您在多個裝置上觸發裝置或雲端屬性更新。 您也可以使用作業來觸發多個裝置上的裝置命令。 Azure IoT Central 會自動為您執行工作流程。 

1. 從 Azure IoT Central 的左側窗格中，選取 [作業]。 
1. 選取 [+新增] 並設定一或多個作業。 

## <a name="customize-your-application"></a>自訂應用程式 

身為建置者，您可以變更數個設定，以自訂應用程式的使用者體驗。

### <a name="change-the-application-theme"></a>變更應用程式主題

其做法如下：
1. 移至 [系統管理] > [自訂您的應用程式]。
1. 選取 [變更] 來選擇要上傳作為 **應用程式標誌** 的影像。
1. 選取 [變更] 以選擇要上傳作為 **瀏覽器圖示** 的影像 (出現在瀏覽器索引標籤上的影像)。
1. 您也可以藉由新增 HTML 十六進位色彩代碼來取代預設的瀏覽器色彩。 基於此目的，請使用 **標頭** 和 **輔色** 欄位。

   ![聯網廢棄物管理範本自訂應用程式的螢幕擷取畫面。](./media/tutorial-connectedwastemanagement/connectedwastemanagement-customize-your-application.png)

1. 您也可以變更應用程式影像。 選取 [系統管理] > [應用程式設定] > [選取影像]，選擇要上傳作為應用程式影像的影像。
1. 最後，您也可以在應用程式的標題上選取 [設定]，以變更主題。

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續使用此應用程式，請使用下列步驟刪除您的應用程式：

1. 在 Azure IoT Central 應用程式的左側窗格中選取 [系統管理]。
1. 選取 [應用程式設定] > [刪除]。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [聯網廢棄物管理概念](./concepts-connectedwastemanagement-architecture.md)
