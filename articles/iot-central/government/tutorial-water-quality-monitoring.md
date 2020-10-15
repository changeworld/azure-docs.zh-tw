---
title: 教學課程：使用 Azure IoT Central 建立水質監視應用程式
description: 教學課程：了解如何使用 Azure IoT Central 應用程式範本來建立水質監視應用程式。
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: a80fe998c1ab241e35bee195b927846e264dec59
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90972363"
---
# <a name="tutorial-create-a-water-quality-monitoring-application-in-azure-iot-central"></a>教學課程：在 Azure IoT Central 中建立水質監視應用程式



本教學課程會引導您在 Azure IoT Central 中建立水質監視應用程式。 您可以從 Azure IoT Central 的**水質監視**應用程式範本中建立應用程式。

您會在本教學課程中學到：

> [!div class="checklist"]
> * 使用**水質監視**範本來建立水質監視應用程式。
> * 探索和自訂操作員儀表板。
> * 探索水質監視裝置範本。
> * 探索模擬裝置。
> * 探索和設定規則。
> * 設定作業。
> * 使用白標自訂應用程式商標。

## <a name="prerequisites"></a>Prerequisites

我們建議您使用 Azure 訂用帳戶來完成本教學課程。 如果您沒有 Azure 訂用帳戶，則可以在 [Azure 註冊頁面](https://aka.ms/createazuresubscription)上建立一個。

## <a name="create-a-water-quality-monitoring-application-in-azure-iot-central"></a>在 Azure IoT Central 中建立水質監視應用程式

在本節中，您將使用 Azure IoT Central 的**水質監視**範本來建立水質監視應用程式。

1. 移至 [Azure IoT Central 首頁](https://aka.ms/iotcentral)。

    如果您有 Azure 訂用帳戶，請使用您存取時所用的認證來登入。 否則，請使用 Microsoft 帳戶來登入：

    ![登入您的組織帳戶](./media/tutorial-waterqualitymonitoring/sign-in.png)

1. 在 Azure IoT Central 最左邊的窗格上選取 [建置]  ，然後選取 [政府]  索引標籤。[政府] 窗格會顯示數個政府應用程式範本。

    ![政府應用程式範本](./media/tutorial-waterqualitymonitoring/iotcentral-government-tab-overview1.png)

1. 選取 [水質監視]  應用程式範本。 此應用程式範本包含水質裝置範本、模擬裝置、操作員儀表板，以及預先設定的監視規則。

1. 選取 [建立應用程式]  。 [新增應用程式]  窗格會隨即開啟，並顯示下列元素：

    * **應用程式名稱**：根據預設，應用程式名稱為 Water quality monitoring  加上 Azure IoT Central 產生的唯一識別碼字串。 如有需要，您可以在稍後輸入顯示名稱或變更應用程式名稱。
    * **URL**：您可以輸入任何想用的 URL，或稍後變更 URL 值。
    * 如果您有 Azure 訂用帳戶，請輸入 [目錄]  、[Azure 訂用帳戶]  和 [區域]  的值。 如果您沒有訂用帳戶，則可以啟用 [7 天免費試用]  並填妥必要的連絡人資訊。

    如需關於目錄和訂用帳戶的詳細資訊，請參閱[建立應用程式快速入門](../core/quick-deploy-iot-central.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)。

1. 選取頁面左下角的 [建立]  按鈕。

    ![Azure IoT Central 的新增應用程式頁面](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1.png)

    ![Azure IoT Central 的新增應用程式計費資訊](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1-billinginfo.png)

您現在已使用 Azure IoT Central 的**水質監視**範本建立水質監視應用程式。

您的新應用程式包含下列預先設定的元件：

* 操作員儀表板
* 水質監視裝置範本
* 模擬水質監視裝置
* 規則及作業
* 使用白標的商標

您隨時都可以修改應用程式。

接下來，我們要探索應用程式，並加入一些自訂內容。

## <a name="explore-and-customize-the-operator-dashboard"></a>探索和自訂操作員儀表板

建立應用程式之後，[Wide World 水質儀表板]  窗格會隨即開啟。

   ![水質監視儀表板](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-dashboard1.png)

身為建置者，您可以在儀表板上建立及自訂操作員適用的檢視。 在您嘗試進行自訂之前，我們先來探索一下儀表板。

儀表板中顯示的所有資料都是以模擬裝置資料為基礎，我們將在下一節中加以討論。

儀表板包含下列類型的磚：

* **水資源公用事業影像磚**：儀表板左上角的第一個磚是一個影像，其中顯示名為 Wide World 的虛構公用事業。 您可以使用您自己的影像來自訂磚，也可以移除磚。

* **平均 pH KPI 磚**：位於儀表板窗格頂端的是 KPI 磚，例如「過去 30 分鐘內的平均 pH 值」  。 您可以自訂 KPI 磚，並為每一項設定不同的類型和時間範圍。

* **水資源監視區域地圖**：Azure IoT Central 使用 Azure 地圖服務，您可以直接在應用程式中進行設定，以顯示裝置位置。 您也可以將應用程式中的位置資訊對應到您的裝置，然後使用 Azure 地圖服務來顯示地圖上的資訊。 將滑鼠停留在地圖上，並嘗試使用控制項。

* **平均 pH 分佈熱度圖**：您可以選取不同的視覺效果圖表，以最適合您應用程式的方式來顯示裝置遙測。

* **重大品質指標折線圖**：您可以將裝置遙測視覺化，以折線圖繪製某個時間範圍內的遙測資料。  

* **化學藥劑濃度的長條圖**：您可以透過長條圖將裝置遙測視覺化。

* **動作按鈕**：儀表板包含動作磚，操作員可以直接從監視儀表板中起始這些動作。 重設裝置屬性是這類動作的範例。

* **屬性清單磚**：儀表板有多個屬性磚，分別代表閾值資訊、裝置健康情況資訊和維護資訊。

### <a name="customize-the-dashboard"></a>自訂儀表板

身為建置者，您可以在儀表板上自訂操作員適用的檢視。

1. 選取 [編輯]  以自訂 [Wide World 水質儀表板]  窗格。 您可以選取 [編輯]  功能表上的命令來自訂儀表板。 當儀表板處於 [編輯] 模式之後，您就可以增加新的磚或設定現有檔案。

    ![編輯您的儀表板](./media/tutorial-waterqualitymonitoring/edit-dashboard.png)

1. 選取 [+ 新增]  ，以建立您可以設定的新儀表板。 您可以有多個儀表板，而且可以從 [儀表板] 功能表中瀏覽這些儀表板。

## <a name="explore-a-water-quality-monitoring-device-template"></a>探索水質監視裝置範本

Azure IoT Central 中的裝置範本會定義裝置的功能。 可用功能包括遙測、屬性和命令。 身為建置者，您可以在 Azure IoT Central 中定義裝置範本，以代表連線裝置的功能。 您也可以建立模擬裝置來測試您的裝置範本和應用程式。

您建立的水質監視應用程式會包含水質監視裝置範本。

若要檢視裝置範本：

1. 在 Azure IoT Central 中應用程式的最左邊窗格上，選取 [裝置範本]  。
1. 從裝置範本清單中，選取 [水質監視器]  。 該裝置範本會隨即開啟。

    ![裝置範本](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate.png)

### <a name="customize-the-device-template"></a>自訂裝置範本

練習自訂下列裝置範本的設定：

1. 在裝置範本功能表中，選取 [自訂]  。
1. 移至**溫度**遙測類型。
1. 將 [顯示名稱]  值變更為**回報的溫度**。
1. 變更測量單位，或設定 [最小值]  和 [最大值] 
1. 選取 [儲存]  。

#### <a name="add-a-cloud-property"></a>新增雲端屬性

1. 在裝置範本功能表中，選取 [雲端屬性]  。
1. 若要新增雲端屬性，請選取 [+ 新增雲端屬性]  。 在 Azure IoT Central 中，您可以新增與裝置相關但不會由裝置傳送的屬性。 這類屬性的其中一個範例是專屬於安裝區域、資產資訊或維護資訊的警示閾值。
1. 選取 [儲存]  。

### <a name="explore-views"></a>探索檢視

水質監視裝置範本會隨附預先定義的檢視。 這些檢視會定義操作員如何查看裝置資料定設定雲端屬性。 探索檢視及練習進行變更。

  ![裝置範本檢視](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate-views.png)

### <a name="publish-the-device-template"></a>發佈裝置範本

如果您執行了任何變更，請務必選取 [發佈]  來發佈裝置範本。

### <a name="create-a-new-device-template"></a>建立新的裝置範本

1. 選取 [+ 新增]  來建立新的裝置範本，並遵循建立程序。
1. 建立自訂裝置範本，或從 Azure IoT 裝置目錄選擇裝置範本。

## <a name="explore-simulated-devices"></a>探索模擬裝置

您從應用程式範本中建立的水質監視應用程式有兩個模擬裝置。 這些裝置會與水質監視裝置範本相對應

### <a name="view-the-devices"></a>檢視裝置

1. 在應用程式的最左邊窗格中，選取 [裝置]  。

   ![裝置](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devices.png)

1. 選取一個模擬裝置。

    ![選取裝置 1](./media/tutorial-waterqualitymonitoring/waterqualitymonitor-device1.png)

1. 在 [雲端屬性]  索引標籤上，將 [酸度 (pH) 閾值]  的值從 **8** 變更為 **9**。
1. 依序瀏覽 [裝置屬性]  索引標籤和 [裝置儀表板]  索引標籤。

> [!NOTE]
> 所有索引標籤都已從 [裝置範本檢視]  中進行設定。

### <a name="add-new-devices"></a>新增裝置

在 [裝置]  索引標籤上，選取 [+ 新增]  來新增裝置。

## <a name="explore-and-configure-rules"></a>探索和設定規則

在 Azure IoT Central 中，您可以建立自動監視裝置遙測的規則。 這些規則會在有任何條件相符時觸發動作。 其中一個可能的動作是傳送電子郵件通知。 其他可能性包括將資料傳送至其他服務的 Microsoft Flow 動作或 Webhook 動作。

您所建立的水質監視應用程式有兩個預先設定的規則。

### <a name="view-rules"></a>檢視規則

1. 在應用程式的最左邊窗格中，選取 [規則]  。

   ![規則](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-rules.png)

1. 選取 [高 pH 值警示]  ，這是應用程式中其中一個預先設定的規則。

   ![高 pH 值警示規則](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-highphalert.png)

   **高 pH 值警示**規則已設定為檢查酸度 (pH) 條件是否大於 8。

接下來，將電子郵件動作新增至規則：

1. 選取 [+ 電子郵件]  。
1. 在 [顯示名稱]  方塊中，輸入**高 pH 值警示**。
1. 在 [收件人]  方塊中，輸入與您 Azure IoT Centra 帳戶相關聯的電子郵件地址。
1. (選擇性) 輸入附註來納入電子郵件的文字。
1. 選取 [完成]  來完成此動作。
1. 選取 [儲存]  以儲存並啟用新的規則。

幾分鐘內，您應該會在設定條件相符時收到電子郵件。

> [!NOTE]
> 應用程式會在每次條件相符時傳送電子郵件。 對規則選取 [停用]  ，可停止接收來自該規則的自動化電子郵件。
  
若要建立新的規則，請在應用程式的最左邊窗格上選取 [規則]  ，然後選取 [+ 新增]  。

## <a name="configure-jobs"></a>設定工作

透過 Azure IoT Central 作業，您可以在多個裝置上觸發裝置或雲端屬性的更新。 您也可以使用作業來觸發多個裝置上的裝置命令。 Azure IoT Central 會自動為您執行工作流程。

1. 在應用程式的最左邊窗格中，選取 [作業]  。
1. 選取 [+新增]  並設定一個或多個作業。

## <a name="customize-your-application"></a>自訂應用程式

身為建置者，您可以變更數個設定，以自訂應用程式的使用者體驗。

1. 選取 [系統管理]   > [自訂您的應用程式]  。
1. 在 [應用程式標誌]  底下選取 [變更]  ，以選擇要上傳為標誌的影像。
1. 在 [瀏覽器圖示]  底下選取 [變更]  ，以選擇出現在瀏覽器索引標籤上的影像。
1. 在 [瀏覽器色彩]  底下，您可以使用 HTML 十六進位色彩代碼來取代預設值。
1. 選取 [設定]  來變更 [主題]  的值。

   ![自訂應用程式](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-customize-your-application1.png)

### <a name="update-the-application-image"></a>更新應用程式影像

1. 選取 [系統管理]   > [應用程式設定]  。

1. 使用 [選取影像]  按鈕來選擇要上傳作為應用程式影像的影像。

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續使用您的應用程式，請使用下列步驟刪除應用程式：

1. 在應用程式最左邊的窗格上，開啟[系統管理]  索引標籤。
1. 選取 [應用程式設定]  ，然後選取 [刪除]  按鈕。

    ![刪除您的應用程式](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-application-settings-delete-app1.png)

## <a name="next-steps"></a>後續步驟

* 深入了解 

> [!div class="nextstepaction"]
> [水質監視概念](./concepts-waterqualitymonitoring-architecture.md)。
