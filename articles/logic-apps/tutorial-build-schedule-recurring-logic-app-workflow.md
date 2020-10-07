---
title: 使用 Azure 建置排程型自動化工作流程
description: 教學課程 - 使用 Azure Logic Apps 建立跨雲端服務整合的排程型週期性自動化工作流程。
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/30/2020
ms.openlocfilehash: 3bf4ad12bab3e71675ff35203bf69526b3b8614f
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91574576"
---
# <a name="tutorial-create-schedule-based-and-recurring-automation-workflows-with-azure-logic-apps"></a>教學課程：使用 Azure Logic Apps 建立排程型和週期性自動化工作流程

本教學課程示範如何建置範例[邏輯應用程式](../logic-apps/logic-apps-overview.md)，將依週期性排程執行的工作流程自動化。 具體而言，此範例邏輯應用程式會檢查兩個地點之間的行進時間 (包括流量)，並在每個工作日上午執行。 如果時間超過特定限制，則邏輯應用程式會向您傳送一封電子郵件，其中包含行進時間和抵達目的地所需的額外時間。 此工作流程包含各種步驟，其會開始使用排程型觸發程序，後面接著 Bing 地圖服務動作、資料作業動作、控制流程動作，以及電子郵件通知動作。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立空白邏輯應用程式。
> * 新增可指定邏輯應用程式排程的週期觸發程序。
> * 新增可取得路線行進時間的 Bing 地圖服務動作。
> * 新增可建立變數、將行進時間從秒鐘轉換為分鐘，並將該結果存放在變數中的動作。
> * 新增可針對指定的限制比較行進時間的條件。
> * 新增可在行進時間超過限制時傳送電子郵件的動作。

當您完成時，邏輯應用程式大致如下列工作流程所示︰

![此螢幕擷取畫面顯示範例邏輯應用程式工作流程的高階概觀。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

## <a name="prerequisites"></a>Prerequisites

* Azure 帳戶和訂用帳戶。 如果您沒有訂用帳戶，請[註冊一個免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* Logic Apps 支援的任何電子郵件提供者 (例如 Office 365 Outlook、Outlook.com 或 Gmail) 所提供的電子郵件帳戶。 對於其他提供者，請[檢閱這裡的連接器清單](/connectors/)。 本快速入門會使用 Office 365 Outlook 搭配公司或學校帳戶。 如果您使用不同的電子郵件帳戶，整體步驟將維持不變，但您的 UI 可能略有不同。

  > [!IMPORTANT]
  > 如果您想要使用 Gmail 連接器，只有 G-Suite 商務帳戶可以在邏輯應用程式中使用此連接器，而不受限制。 如果您有 Gmail 取用者帳戶，您只能使用此連接器搭配特定的 Google 核准服務，或者您可以[建立 Google 用戶端應用程式，以用來向 Gmail 連接器進行驗證](/connectors/gmail/#authentication-and-bring-your-own-application)。 如需詳細資訊，請參閱 [Azure Logic Apps 中 Google 連接器的資料安全性和隱私權原則](../connectors/connectors-google-data-security-privacy-policy.md)。

* 若要取得路線的行進時間，您需要 Bing 地圖服務 API 的存取金鑰。 若要取得此金鑰，請遵循[如何取得 Bing 地圖服務金鑰](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key)的步驟。

## <a name="create-your-logic-app"></a>建立邏輯應用程式

1. 使用您的 Azure 帳戶認證登入 [Azure 入口網站](https://portal.azure.com)。 在 Azure 首頁上，選取 [建立資源]。

1. 在 Azure Marketplace 功能表上，選取 [整合] > [邏輯應用程式]。

   ![此螢幕擷取畫面顯示已選取 [整合] 和 [邏輯應用程式] 的 Azure Marketplace 功能表。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-new-logic-app-resource.png)

1. 在 [邏輯應用程式] 窗格上，提供這裡所述的資訊，其描述您想要建立的邏輯應用程式。

   ![此螢幕擷取畫面顯示邏輯應用程式建立窗格，以及要提供給新邏輯應用程式的資訊。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | 屬性 | 值 | 描述 |
   |----------|-------|-------------|
   | **訂用帳戶** | <*Azure-subscription-name*> | 您的 Azure 訂用帳戶名稱。 此範例會使用 `Pay-As-You-Go`。 |
   | **資源群組** | LA-TravelTime-RG | [Azure 資源群組](../azure-resource-manager/management/overview.md)的名稱，其用來組織相關資源。 此範例會建立新的資源群組，名為 `LA-TravelTime-RG`。 |
   | **名稱** | LA-TravelTime | 您的邏輯應用程式名稱，其中只能包含字母、數字、連字號 (`-`)、底線 (`_`)、括弧(`(`、`)`) 和句點 (`.`)。 此範例會使用 `LA-TravelTime`。 |
   | **位置** | 美國西部 | 用來存放邏輯應用程式資訊的區域。 此範例會使用 `West US`。 |
   | **Log Analytics** | 關閉 | 保留診斷記錄的 [關閉] 設定。 |
   ||||

1. 當您完成時，選取 [檢閱 + 建立]。 在 Azure 驗證邏輯應用程式的相關資訊之後，請選取 [建立]。

1. 在 Azure 部署您的應用程式之後，請選取 [移至資源]。

   Azure 會開啟 Logic Apps 範本選取窗格，其中顯示簡介影片、常用的觸發程序，以及邏輯應用程式範本模式。

1. 向下捲動影片和觸發程序直到 [範本]區段，然後選取 [空白邏輯應用程式]。

   ![此螢幕擷取畫面顯示已選取 [空白邏輯應用程式] 的 Logic Apps 範本選取窗格。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-logic-app-template.png)

接下來，新增週期[觸發程序](../logic-apps/logic-apps-overview.md#logic-app-concepts)，此觸發程序會根據指定的排程執行。 每個邏輯應用程式都必須使用觸發程序啟動，而該觸發程序會在特定事件發生或新資料符合特定條件時引發。 如需詳細資訊，請參閱[建立您的第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="add-the-recurrence-trigger"></a>新增週期觸發程序

1. 在 [Logic Apps 設計工具] 搜尋方塊中，輸入 `recurrence`，然後選取名為 **Recurrence** 的觸發程序。

   ![此螢幕擷取畫面顯示 [Logic Apps 設計工具] 搜尋方塊，其中包含「週期」搜尋詞彙並在 [觸發程序] 清單中出現選取的「週期」觸發程序。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

1. 在 [週期] 圖形中，選取**省略符號** ( **...** ) 按鈕，然後選取 [重新命名]。 以下列描述為觸發程序重新命名：`Check travel time every weekday morning`

   ![此螢幕擷取畫面顯示選取的省略符號按鈕、開啟的 [設定] 清單，以及選取的 [重新命名] 命令。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

1. 在觸發程序內，變更這些屬性，如下所述和所示。

   ![此螢幕擷取畫面顯示觸發程序間隔和頻率的變更。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/change-interval-frequency.png)

   | 屬性 | 必要 | 值 | 說明 |
   |----------|----------|-------|-------------|
   | **間隔** | 是 | 1 | 在檢查之間所要等待的間隔數目 |
   | **頻率** | 是 | 週 | 要用於週期的時間單位 |
   |||||

1. 在 [間隔] 和 [頻率] 底下，開啟 [新增參數] 清單，然後選取要新增至觸發程序的這些屬性。

   * **在這幾天內**
   * **在這幾小時內**
   * **在這幾分鐘內**

   ![此螢幕擷取畫面顯示已開啟的 [新增參數] 清單，以及這些選取的屬性：「在這幾天內」、「在這幾個小時內」和「在這幾分鐘內」。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-trigger-properties.png)

1. 現在設定其他屬性的值，如下所示及說明。

   ![此螢幕擷取畫面顯示已設定為如下表所述值的其他屬性。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/recurrence-trigger-property-values.png)

   | 屬性 | 值 | 說明 |
   |----------|-------|-------------|
   | **在這幾天內** | 星期一、星期二、星期三、星期四、星期五 | 只在您將 [頻率] 設定為 [周] 時，才可以使用此設定。 |
   | **在這幾小時內** | 7、8、9 | 只在您將 [頻率] 設定為 [周] 或 [日] 時，才可以使用此設定。 對於此週期，選取幾小時。 這個範例會在上午 `7`、`8` 和 `9` 整點執行。 |
   | **在這幾分鐘內** | 0、15、30、45 | 只在您將 [頻率] 設定為 [周] 或 [日] 時，才可以使用此設定。 對於此週期，選取幾分鐘。 這個範例從整點開始，每隔 15 分鐘執行一次。 |
   ||||

   此觸發程序會在每個工作天、每隔 15 分鐘引發一次 (從上午 7:00 開始到上午 9:45 結束)。 [預覽] 方塊會顯示週期排程。 如需詳細資訊，請參閱[排程工作和工作流程](../connectors/connectors-native-recurrence.md)及[工作流程動作和觸發程序](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger)。

1. 若要立即隱藏觸發程序的詳細資料，請按一下圖形的標題列內部來摺疊圖形。

   ![此螢幕擷取畫面顯示已摺疊的觸發程序圖形。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/collapse-trigger-shape.png)

1. 儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]。

您的邏輯應用程式現在會存留在 Azure 入口網站中，但不會根據指定的排程執行觸發程序以外的任何項目。 因此，新增會在觸發程序引發時回應的動作。

## <a name="get-the-travel-time-for-a-route"></a>取得路線的行進時間

有了觸發程序之後，請新增可取得兩地間行進時間的[動作](../logic-apps/logic-apps-overview.md#logic-app-concepts)。 Logic Apps 會提供 Bing 地圖服務 API 的連接器，以便輕鬆地取得此資訊。 在開始這項工作之前，請確定您擁有本教學課程必要條件中所述的 Bing 地圖服務 API 金鑰。

1. 在 Logic Apps 設計工具中，於週期觸發程序之下，選取 [新增步驟]。

1. 在 [選擇作業] 底下，選取 [標準]。 在搜尋方塊中，輸入 `bing maps`，然後選取名為 [取得路線] 的動作。

   ![此螢幕擷取畫面顯示已依 [Bing 地圖服務] 動作篩選 [選擇作業] 清單，並已選取 [取得路線] 動作。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-get-route-action.png)

1. 如果您沒有 Bing 地圖服務連線，系統會要求您建立連線。 提供如下所示及所述的連線詳細資料，然後選取 [建立]。

   ![此螢幕擷取畫面顯示 Bing 地圖服務連線方塊，其中具有已指定的連線名稱和 Bing 地圖服務 API 金鑰。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | 屬性 | 必要 | 值 | 描述 |
   |----------|----------|-------|-------------|
   | **連接名稱** | 是 | BingMapsConnection | 為您的連線提供一個名稱。 此範例會使用 `BingMapsConnection`。 |
   | **API 金鑰** | 是 | <*Bing-Maps-API-key*> | 輸入您先前收到的 Bing 地圖服務 API 金鑰。 如果您沒有 Bing 地圖服務金鑰，請了解[如何取得金鑰](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key)。 |
   |||||

1. 以下列描述為動作重新命名：`Get route and travel time with traffic`。

1. 在動作中，開啟 [新增參數清單]，然後選取這些屬性。

   * **最佳化**
   * **距離單位**
   * **行進模式**

   ![此螢幕擷取畫面顯示已選取 [最佳化]、[距離單位] 和 [行進模式] 屬性的 [取得路線...] 動作。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-bing-maps-action-properties.png) 

1. 現在輸入屬性的值，如下所示及所述。

   ![此螢幕擷取畫面顯示 [取得路線] 動作的其他屬性值。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | 屬性 | 必要 | 值 | 描述 |
   |----------|----------|-------|-------------|
   | **導航點 1** | 是 | <*start-location*> | 您的路線起點。 這個範例會指定範例起始位址。 |
   | **導航點 2** | 是 | <*end-location*> | 您的路線目的地。 這個範例會指定範例目的地位址。 |
   | **最佳化** | 否 | timeWithTraffic | 可將您的路線最佳化的參數，例如距離、目前流量的行進時間等等。 選取參數值 **timeWithTraffic**。 |
   | **距離單位** | 否 | <*your-preference*> | 路線的距離單位。 此範例使用**英哩**作為單位。 |
   | **行進模式** | 否 | 開車 | 路線的行進模式。 選取 [開車] 模式。 |
   |||||

   如需這些參數及值的詳細資訊，請參閱[計算路線](/bingmaps/rest-services/routes/calculate-a-route)。

1. 在設計工具的工具列上，選取 [儲存]。

接下來，建立一個變數，以便將目前行進時間轉換為分鐘 (而不是秒鐘) 並加以儲存。 如此一來，即可避免重複轉換，並且在後續步驟中更輕鬆使用此值。 

## <a name="create-a-variable-to-store-travel-time"></a>建立變數來儲存行進時間

有時候，您可以在工作流程中對資料執行作業，然後在後續動作中使用其結果。 若要儲存這些結果，以便輕鬆地重複使用或參考，您可以建立一些變數，在處理後儲存這些結果。 您只能在邏輯應用程式的最上層建立變數。

根據預設，[取得路線] 動作會經由 [行進持續時間流量] 屬性，傳回包含流量的目前行進時間 (以秒為單位)。 藉由轉換此值並改以分鐘為單位進行儲存，即可讓此值日後更容易重複使用，而不需再次轉換。

1. 在設計工具上，於 [取得路線] 動作底下，選取 [新增步驟]。

1. 在 [選擇作業] 底下，選取 [內建]。 在搜尋方塊中，輸入 `variables`，然後選取名為 [初始化變數] 的動作。

   ![顯示已選取 [初始化變數] 動作的螢幕擷取畫面。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

1. 以下列描述為動作重新命名：`Create variable to store travel time`

1. 為您的變數提供這項資訊，如本表以及表格下方的步驟所示：

   | 屬性 | 必要 | 值 | 描述 |
   |----------|----------|-------|-------------|
   | **名稱** | 是 | travelTime | 您的變數名稱。 此範例會使用 `travelTime`。 |
   | **型別** | 是 | 整數 | 您的變數資料類型 |
   | **ReplTest1** | 否 | 可將目前行進時間從秒數轉換為分鐘數的運算式 (請參閱此表格底下的步驟)。 | 您的變數初始值 |
   |||||

   1. 若要建立 [值] 屬性的運算式，請按一下此方塊內部，動態內容清單隨即出現。 必要時，讓您的瀏覽器變寬，直到動態清單出現為止。 在動態內容清單中，選取 [運算式]，這會顯示運算式編輯器。

      ![此螢幕擷取畫面顯示 [值] 屬性內具有游標的 [初始化變數] 動作，這會開啟動態內容清單。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      動態內容清單會顯示先前動作的輸出，供您選取做為工作流程中後續動作的輸入。 動態內容清單包含一個運算式編輯器，您可以用來選取在運算式中執行作業的函式。 此運算式編輯器只能在動態內容清單中使用。

   1. 在運算式編輯器中，輸入此運算式：`div(,60)`

      ![此螢幕擷取畫面顯示已輸入 "div(,60)" 運算式的運算式編輯器。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   1. 在運算式內，將游標放在左括號 ( **(** ) 與逗號 ( **,** ) 之間，然後選取 [動態內容]。

      ![此螢幕擷取畫面顯示在選取 [動態內容] 的情況下，要在 "div(,60)" 運算式中的哪個位置放置游標。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   1. 在動態內容清單中，選取屬性值 [行進持續時間流量]。

      ![顯示已選取 [行進持續時間流量] 屬性值的螢幕擷取畫面。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   1. 在運算式內解析此屬性值之後，請選取 [確定]。

      ![顯示已選取 [確定] 按鈕的螢幕擷取畫面。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      [值] 屬性隨即出現，如下所示：

      ![此螢幕擷取畫面顯示具有已解析運算式的 [值] 屬性。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

1. 儲存您的邏輯應用程式。

接下來，新增可檢查目前行進時間是否大於特定限制的條件。

## <a name="compare-the-travel-time-with-limit"></a>比較行進時間與限制

1. 在 [建立變數以儲存行進時間] 動作下，選取 [新增步驟]。

1. 在 [選擇作業] 底下，選取 [內建]。 在搜尋方塊中，輸入 `condition`。 從 [動作] 清單中，選取名為 [條件] 的動作。

   ![顯示已選取 [條件] 動作的螢幕擷取畫面](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-condition-action.png)

1. 以下列描述為條件重新命名：`If travel time exceeds limit`

1. 建立一個條件，以檢查 **travelTime** 屬性值是否超過您指定的限制，如下所描述和顯示：

   1. 在條件內，按一下條件左側的 [選擇值] 方塊內部。

   1. 從顯示的動態內容清單中，選取 [變數] 底下名為 [travelTime] 的屬性。

      ![此螢幕擷取畫面顯示條件左側的 [選擇值] 方塊，其中已開啟動態內容清單並選取 [travelTime] 屬性。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-left-side.png)

   1. 在中間比較方塊中，選取名為 [大於] 的運算子。

   1. 在條件右側的 [選擇值] 方塊中，輸入此限制：`15`

      當您完成時，條件看起來就像下面這個範例︰

      ![此螢幕擷取畫面顯示用於比較行進時間與所指定限制的已完成條件。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time.png)

1. 儲存您的邏輯應用程式。

接下來，新增當行進時間超過限制時所要執行的動作。

## <a name="send-email-when-limit-exceeded"></a>在超過限制時傳送電子郵件

現在，新增可在行進時間超過限制時傳送電子郵件給您的動作。 此電子郵件包含目前行進時間和行經指定路線所需的額外時間。

1. 在條件的 [True] 分支中，選取 [新增動作]。

1. 在 [選擇作業] 底下，選取 [標準]。 在搜尋方塊中，輸入 `send email`。 此清單會傳回許多結果，因此若要協助您篩選清單，請先選取您想要的電子郵件連接器。

   例如，如果您擁有 Outlook 電子郵件帳戶，請為您的帳戶類型選取連接器：

   * 對於 Azure 工作或學校帳戶，選取 [Office 365 Outlook]。
   * 對於個人 Microsoft 帳戶，選取 [Outlook.com]。

   此範例會選取 Office 365 Outlook 以繼續。

   ![此螢幕擷取畫面顯示已選取 [標準] 分類和 [Office 365 Outlook] 連接器的 [選擇作業清單]。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

1. 當連接器的動作出現時，請選取傳送電子郵件的動作，例如：

   ![顯示已選取 [傳送電子郵件] 動作的螢幕擷取畫面。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-send-email-action.png)

1. 如果您還沒有連線，請在出現提示時登入並驗證電子郵件帳戶的存取權。

   Azure Logic Apps 會建立與電子郵件帳戶的連線。

1. 以下列描述為動作重新命名：`Send email with travel time`

1. 針對 [收件者] 屬性，輸入收件者的電子郵件地址。 為了測試用途，您可以使用自己的電子郵件地址。

1. 針對 [主旨] 屬性，遵循下列步驟指定電子郵件的主旨，並包含 [travelTime] 變數：

   1. 輸入尾端有一個空格的文字 `Current travel time (minutes):`。 將游標保留在 [主旨] 方塊中，讓動態內容清單保持開啟狀態。

   1. 從動態內容清單中，於 [變數] 標題中，選取 [查看更多]，以便顯示名為 [travelTime] 的變數。

      ![此螢幕擷取畫面顯示已選取 [變數] 區段和 [查看更多] 的動態內容清單。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-travelTime-variable.png)

      > [!NOTE]
      > 動態內容清單不會自動顯示 [travelTime] 變數，因為 [主旨] 屬性預期字串值，而 [travelTime] 卻是整數值。

      ![此螢幕擷取畫面顯示已選取 [travelTime] 變數的動態內容清單。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-travelTime-variable.png)

1. 針對 [內文] 屬性，遵循下列步驟指定電子郵件內文的內容：

   1. 輸入尾端有一個空格的文字 `Add extra travel time (minutes):`。 將游標保留在 [內文] 方塊中，讓動態內容清單保持開啟狀態。

   1. 在動態內容清單中，選取 [運算式]，這會顯示運算式編輯器。

      ![此螢幕擷取畫面顯示已選取 [運算式] 的動態內容清單。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   1. 在運算式編輯器中，輸入 `sub(,15)`，以便您可以計算超過限制的分鐘數： 

      ![此螢幕擷取畫面顯示已輸入 "sub(,15)" 運算式的運算式編輯器。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   1. 在運算式內，將游標放在左括號 ( **(** ) 與逗號 ( **,** ) 之間，然後選取 [動態內容]。

      ![此螢幕擷取畫面顯示在選取 [動態內容] 的情況下，要在 "sub(,15)" 運算式中的哪個位置放置游標。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   1. 在 [變數] 下方，選取 [travelTime]。

      ![此螢幕擷取畫面顯示已選取 [travelTime] 變數的動態內容清單。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   1. 在運算式內解析此屬性之後，請選取 [確定]。

      ![顯示動態內容清單並已選取 [確定] 的螢幕擷取畫面。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      [Body] 屬性隨即出現，如下所示：

      ![此螢幕擷取畫面顯示已在電子郵件動作 [內文] 屬性中解析運算式的動態內容清單。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

1. 儲存您的邏輯應用程式。

接著，請測試並執行您的邏輯應用程式，此時它看起來類似下列範例：

![此螢幕擷取畫面顯示已完成的範例邏輯應用程式工作流程](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>執行邏輯應用程式

若要手動啟動邏輯應用程式，請在設計工具的工具列上選取 [執行]。

* 如果目前行進時間保持在您的限制之內，邏輯應用程式並不會執行任何作業，而會等到下一個間隔後再次檢查。 

* 如果目前行進時間超過您的限制，您會收到一封電子郵件，其中包含目前行進時間和超過限制的分鐘數。 以下是邏輯應用程式傳送的範例電子郵件：

  ![此螢幕擷取畫面顯示範例電子郵件，其會報告目前的行進時間和超出所指定限制的額外行進時間。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/received-example-email-notification.png)

  > [!TIP]
  > 若未收到任何電子郵件，請檢查電子郵件的垃圾郵件資料夾。 您的垃圾電子郵件篩選器可能會重新導向這類郵件。 或者，如果您不確定邏輯應用程式是否正確執行，請參閱[針對邏輯應用程式進行疑難排解](../logic-apps/logic-apps-diagnosing-failures.md)。

恭喜，您已經建立並執行以排程為基礎的週期性邏輯應用程式。 

若要建立使用 [週期] 觸發程序的其他邏輯應用程式，請簽出在您建立邏輯應用程式之後可用的下列範本：

* 讓每日提醒傳送給您。
* 刪除較舊的 Azure blob。
* 將訊息新增至 Azure 儲存體佇列。

## <a name="clean-up-resources"></a>清除資源

您的邏輯應用程式會繼續執行，直到您停用或刪除應用程式為止。 如果不再需要範例邏輯應用程式，請刪除包含邏輯應用程式的資源群組和相關資源。

1. 在 Azure 入口網站的搜尋方塊中，輸入您所建立的資源群組名稱。 從結果中，在 [資源群組] 底下，選取資源群組。

   此範例已建立名為 `LA-TravelTime-RG` 的資源群組。 

   ![此螢幕擷取畫面顯示已輸入 "la-travel-time-rg" 並已選取 **LA-TravelTime-RG** 的 Azure 搜尋方塊。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-resource-group.png)

   > [!TIP]
   > 如果 Azure 首頁顯示 [最近的資源] 底下的資源群組，您可以從首頁中選取群組。

1. 在資源群組功能表上，檢查是否已選取 [概觀]。 在 [概觀] 窗格的工具列上，選取 [刪除資源群組]。 

   ![此螢幕擷取畫面顯示資源群組的 [概觀] 窗格，並在窗格的工具列上選取了 [刪除資源群組]。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

1. 在確認窗格中，輸入資源群組名稱，然後選取 [刪除]。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立一個邏輯應用程式，該邏輯應用程式可根據指定的排程 (在工作日上午) 檢查流量，並且在行進時間超過指定的限制時採取動作 (傳送電子郵件)。 現在，藉由 Azure 服務、Microsoft 服務和其他軟體即服務 (SaaS) 應用程式的整合，了解如何建立可傳送郵寄清單要求以供核准的邏輯應用程式。

> [!div class="nextstepaction"]
> [管理郵寄清單要求](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
