---
title: 建置以排程為基礎的自動化工作流程
description: 教學課程 - 使用 Azure Logic Apps 建立以排程為基礎的週期性自動化工作流程
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/12/2019
ms.openlocfilehash: 5d4990fd806aed75d9b5e5ddd3e9a615631d9d65
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82146514"
---
# <a name="tutorial-create-automated-schedule-based-recurring-workflows-by-using-azure-logic-apps"></a>教學課程：使用 Azure Logic Apps 建立以排程為基礎的週期性自動化工作流程

本教學課程示範如何建置[邏輯應用程式](../logic-apps/logic-apps-overview.md)，並將依排程執行的週期性工作流程自動化。 具體而言，此範例邏輯應用程式會在每個工作日上午執行，並檢查兩個地點之間的行進時間，包括流量。 如果時間超過特定限制，則邏輯應用程式會傳送包含行進時間和前往目的地所需額外時間的電子郵件。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立空白邏輯應用程式。
> * 新增可指定邏輯應用程式排程的週期觸發程序。
> * 新增可取得路線行進時間的 Bing 地圖服務動作。
> * 新增可建立變數、將行進時間從秒鐘轉換為分鐘，並將該結果存放在變數中的動作。
> * 新增可針對指定的限制比較行進時間的條件。
> * 新增可在行進時間超過限制時傳送電子郵件的動作。

當您完成時，邏輯應用程式大致如下列工作流程所示︰

![高階邏輯應用程式工作流程概觀](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果沒有訂用帳戶，請在開始前，先[註冊一個免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* Logic Apps 支援的任何電子郵件提供者 (例如 Office 365 Outlook、Outlook.com 或 Gmail) 所提供的電子郵件帳戶。 對於其他提供者，請[檢閱這裡的連接器清單](https://docs.microsoft.com/connectors/)。 本快速入門會使用 Office 365 Outlook 帳戶。 如果您使用不同的電子郵件帳戶，整體步驟將維持不變，但您的 UI 可能略有不同。

  > [!IMPORTANT]
  > 如果您想要使用 Gmail 連接器，只有 G-Suite 商務帳戶可以在邏輯應用程式中使用此連接器，而不受限制。 如果您有 Gmail 取用者帳戶，您只能使用此連接器搭配特定的 Google 核准服務，或者您可以[建立 Google 用戶端應用程式，以用來向 Gmail 連接器進行驗證](https://docs.microsoft.com/connectors/gmail/#authentication-and-bring-your-own-application)。 如需詳細資訊，請參閱 [Azure Logic Apps 中 Google 連接器的資料安全性和隱私權原則](../connectors/connectors-google-data-security-privacy-policy.md)。

* 若要取得路線的行進時間，您需要 Bing 地圖服務 API 的存取金鑰。 若要取得此金鑰，請遵循[如何取得 Bing 地圖服務金鑰](https://docs.microsoft.com/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key)的步驟。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

使用您的 Azure 帳戶認證登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="create-your-logic-app"></a>建立邏輯應用程式

1. 在主要 Azure 功能表中，選取 [建立資源]   > [整合]   > [邏輯應用程式]  。

   ![建立邏輯應用程式資源](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-new-logic-app-resource.png)

1. 在 [建立邏輯應用程式]  下，提供有關於邏輯應用程式的這項資訊，如下所示和描述。 當您完成時，選取 [建立]  。

   ![提供邏輯應用程式的相關資訊](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | 屬性 | 值 | 描述 |
   |----------|-------|-------------|
   | **名稱** | LA-TravelTime | 您的邏輯應用程式名稱，其中只能包含字母、數字、連字號 (`-`)、底線 (`_`)、括弧(`(`、`)`) 和句點 (`.`)。 此範例使用 "LA-TravelTime"。 |
   | **訂用帳戶** | <your-Azure-subscription-name  > | 您的 Azure 訂用帳戶名稱 |
   | **資源群組** | LA-TravelTime-RG | [Azure 資源群組](../azure-resource-manager/management/overview.md)的名稱，其用來組織相關資源。 此範例使用 "LA-TravelTime-RG"。 |
   | **位置** | 美國西部 | 用來存放邏輯應用程式資訊的區域。 此範例使用「美國西部」。 |
   | **Log Analytics** | 關閉 | 保留診斷記錄的 [關閉]  設定。 |
   ||||

1. 在 Azure 部署您的應用程式之後，請在 Azure 工具列上針對您部署的邏輯應用程式選取 [通知]   > [移至資源]  。

   ![移至您的新邏輯應用程式資源](./media/tutorial-build-scheduled-recurring-logic-app-workflow/go-to-new-logic-app-resource.png)

   或者，您可以在搜尋方塊中輸入名稱，以尋找並選取您的邏輯應用程式。

   Logic Apps 設計工具會開啟並顯示含有簡介影片和常用觸發程序和邏輯應用程式模式的頁面。 在 [範本]  底下，選取 [空白邏輯應用程式]  。

   ![選取空白邏輯應用程式範本](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-logic-app-template.png)

接下來，新增週期[觸發程序](../logic-apps/logic-apps-overview.md#logic-app-concepts)，該觸發程序會根據指定的排程引發。 每個邏輯應用程式都必須使用觸發程序啟動，而該觸發程序會在特定事件發生或新資料符合特定條件時引發。 如需詳細資訊，請參閱[建立您的第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="add-the-recurrence-trigger"></a>新增週期觸發程序

1. 在 Logic Apps 設計工具的搜尋方塊中，輸入「週期」作為篩選條件。 從 [觸發程序]  清單中，選取 [週期]  觸發程序。

   ![新增「週期性」觸發程序](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

1. 在 [週期]  圖形中，選取**省略符號** ( **...** ) 按鈕，然後選取 [重新命名]  。 以下列描述為觸發程序重新命名：`Check travel time every weekday morning`

   ![重新命名循環觸發程序描述](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

1. 在觸發程序內，變更這些屬性。

   ![變更循環觸發程序的間隔和頻率](./media/tutorial-build-scheduled-recurring-logic-app-workflow/change-interval-frequency.png)

   | 屬性 | 必要 | 值 | 描述 |
   |----------|----------|-------|-------------|
   | **間隔** | 是 | 1 | 在檢查之間所要等待的間隔數目 |
   | **頻率** | 是 | 週 | 要用於週期的時間單位 |
   |||||

1. 在 [間隔]  和 [頻率]  底下，開啟 [新增參數]  清單，然後選取要新增至觸發程序的這些屬性。

   * **在這幾天內**
   * **在這幾小時內**
   * **在這幾分鐘內**

   ![新增循環觸發程序的屬性](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-trigger-properties.png)

1. 現在設定其他屬性的值，如下所示及說明。

   ![提供排程和週期詳細資料](./media/tutorial-build-scheduled-recurring-logic-app-workflow/recurrence-trigger-property-values.png)

   | 屬性 | 值 | 描述 |
   |----------|-------|-------------|
   | **在這幾天內** | 星期一、星期二、星期三、星期四、星期五 | 僅適用於 [頻率]  設定為 [週] 時。 |
   | **在這幾小時內** | 7、8、9 | 僅適用於 [頻率]  設定為 [週] 或 [天] 時。 選取要執行此週期的整點。 這個範例會在上午 7、8 和 9 點整點執行。 |
   | **在這幾分鐘內** | 0、15、30、45 | 僅適用於 [頻率]  設定為 [週] 或 [天] 時。 選取要執行此週期的時刻。 這個範例會每隔 15 分鐘執行一次 (從整點開始算起)。 |
   ||||

   此觸發程序會在每個工作天、每隔 15 分鐘引發一次 (從上午 7:00 開始到上午 9:45 結束)。 [預覽]  方塊會顯示週期排程。 如需詳細資訊，請參閱[排程工作和工作流程](../connectors/connectors-native-recurrence.md)及[工作流程動作和觸發程序](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger)。

1. 若要立即隱藏觸發程序的詳細資料，請按一下圖形的標題列內部。

   ![摺疊圖形以隱藏詳細資料](./media/tutorial-build-scheduled-recurring-logic-app-workflow/collapse-trigger-shape.png)

1. 儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]  。

邏輯應用程式目前作用中，但不會執行週期以外的任何其他作業。 因此，新增會在觸發程序引發時回應的動作。

## <a name="get-the-travel-time-for-a-route"></a>取得路線的行進時間

有了觸發程序之後，請新增可取得兩地間行進時間的[動作](../logic-apps/logic-apps-overview.md#logic-app-concepts)。 Logic Apps 會提供 Bing 地圖服務 API 的連接器，以便輕鬆地取得此資訊。 在開始這項工作之前，請確定您擁有本教學課程必要條件中所述的 Bing 地圖服務 API 金鑰。

1. 在 Logic Apps 設計工具中，於您的觸發程序之下，選取 [新增步驟]  。

1. 在 [選擇動作]  底下，選取 [標準]  。 在搜尋方塊中，輸入「bing 地圖服務」作為篩選條件，然後選取 [取得路徑]  動作。

   ![選取 [取得路線] 動作](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-get-route-action.png)

1. 如果您沒有 Bing 地圖服務連線，系統會要求您建立連線。 提供以下連線詳細資料，然後選取 [建立]  。

   ![建立與 Bing 地圖服務 API 的連線](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | 屬性 | 必要 | 值 | 描述 |
   |----------|----------|-------|-------------|
   | **連接名稱** | 是 | BingMapsConnection | 為您的連線提供一個名稱。 此範例使用 "BingMapsConnection"。 |
   | **API 金鑰** | 是 | <*your-Bing-Maps-key*> | 輸入您先前收到的 Bing 地圖服務金鑰。 如果您沒有 Bing 地圖服務金鑰，請了解[如何取得金鑰](https://msdn.microsoft.com/library/ff428642.aspx)。 |
   |||||

1. 以下列描述為動作重新命名：`Get route and travel time with traffic`

1. 在動作內，開啟 [新增參數]  清單，然後選取要新增至此動作的這些屬性。

   * **最佳化**
   * **距離單位**
   * **行進模式**

   ![將屬性新增至 [取得路由] 動作](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-bing-maps-action-properties.png) 

1. 現在設定動作屬性的值，如下所示及說明。

   ![提供 [取得路線] 動作的詳細資料](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | 屬性 | 必要 | 值 | 描述 |
   |----------|----------|-------|-------------|
   | **導航點 1** | 是 | <*start-location*> | 您的路線起點 |
   | **導航點 2** | 是 | <*end-location*> | 您的路線目的地 |
   | **最佳化** | 否 | timeWithTraffic | 可將您的路線最佳化的參數，例如距離、目前流量的行進時間等等。 選取 "timeWithTraffic" 參數。 |
   | **距離單位** | 否 | <*your-preference*> | 路線的距離單位。 此範例使用「英哩」作為單位。 |
   | **行進模式** | 否 | 開車 | 路線的行進模式。 選取 [開車] 模式。 |
   ||||

   如需這些參數的詳細資訊，請參閱[計算路線](https://docs.microsoft.com/bingmaps/rest-services/routes/calculate-a-route)。

1. 儲存您的邏輯應用程式。

接下來，建立一個變數，以便將目前行進時間轉換為分鐘 (而不是秒鐘) 並加以儲存。 如此一來，即可避免重複轉換，並且在後續步驟中更輕鬆使用此值。 

## <a name="create-a-variable-to-store-travel-time"></a>建立變數來儲存行進時間

有時候，您可以在工作流程中對資料執行作業，然後在後續動作中使用其結果。 若要儲存這些結果，以便輕鬆地重複使用或參考，您可以建立一些變數，以在處理後儲存這些結果。 您只能在邏輯應用程式的最上層建立變數。

根據預設，前一個 [取得路線]  動作會經由 [行進持續時間流量]  屬性，傳回包含流量的目前行進時間 (以秒為單位)。 藉由轉換此值並改以分鐘為單位進行儲存，即可讓此值日後更容易重複使用，而不需再次轉換。

1. 在 [取得路線]  動作底下，選取 [新增步驟]  。

1. 在 [選擇動作]  底下，選取 [內建]  。 在搜尋方塊中輸入「變數」，然後選取 [初始化變數]  動作。

   ![選取 [初始化變數] 動作](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

1. 以下列描述為動作重新命名：`Create variable to store travel time`

1. 為您的變數提供如下所述的詳細資料：

   | 屬性 | 必要 | 值 | 描述 |
   |----------|----------|-------|-------------|
   | **名稱** | 是 | travelTime | 您的變數名稱。 此範例使用 "travelTime"。 |
   | **型別** | 是 | 整數  | 您的變數資料類型 |
   | **ReplTest1** | 否| 可將目前行進時間從秒數轉換為分鐘數的運算式 (請參閱此表格底下的步驟)。 | 您的變數初始值 |
   ||||

   1. 若要建立 [值]  屬性的運算式，請按一下此方塊內部，動態內容清單隨即出現。 必要時，讓您的瀏覽器變寬，直到清單出現為止。 在動態內容清單中，選取 [運算式]  。

      ![提供 [初始化變數] 動作的資訊](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      當您在某些編輯方塊內按一下時，動態內容清單隨即出現。 這份清單會顯示先前動作中可作為工作流程中輸入的任何屬性。 動態內容清單有一個運算式編輯器，您可以在其中選取用來執行作業的函式。 此運算式編輯器只會顯示在動態內容清單中。

   1. 在運算式編輯器中，輸入此運算式：`div(,60)`

      ![輸入此運算式："div(,60)"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   1. 將游標放在運算式內部的左括號 ( **(** ) 與逗號 ( **,** ) 之間。 
   選取 [動態內容]  。

      ![放置游標，選取 [動態內容]](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   1. 在動態內容清單中，選取 [行進持續時間流量]  。

      ![選取 [行進持續時間流量] 屬性](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   1. 在運算式內解析此屬性值之後，請選取 [確定]  。

      ![若要完成運算式的建置，請選取 [確定]](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      [值]  屬性隨即出現，如下所示：

      ![[值] 屬性會與已解析的運算式一起出現](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

1. 儲存您的邏輯應用程式。

接下來，新增可檢查目前行進時間是否大於特定限制的條件。

## <a name="compare-the-travel-time-with-limit"></a>比較行進時間與限制

1. 在上一個動作底下，選取 [新增步驟]  。

1. 在 [選擇動作]  底下，選取 [內建]  。 在搜尋方塊中，輸入「條件」作為篩選條件。 從動作清單中，選取 [條件]  動作。

   ![選取 [條件] 動作](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-condition-action.png)

1. 以下列描述為條件重新命名：`If travel time exceeds limit`

1. 建立一個條件，以檢查 **travelTime** 屬性值是否超過您指定的限制，如下所描述和顯示：

   1. 在條件內，按一下條件左側的 [選擇值]  方塊內部。

   1. 從顯示的動態內容清單中，選取 [變數]  底下的 [travelTime]  屬性。

      ![建置條件的左側](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-left-side.png)

   1. 在中間的比較方塊中，選取 [大於]  運算子。

   1. 在條件右側的 [選擇值]  方塊中，輸入此限制：`15`

      當您完成時，條件看起來就像下面這個範例︰

      ![完成的檢查移動時間條件](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time.png)

1. 儲存您的邏輯應用程式。

接下來，新增當行進時間超過限制時所要執行的動作。

## <a name="send-email-when-limit-exceeded"></a>在超過限制時傳送電子郵件

現在，新增可在行進時間超過限制時傳送電子郵件給您的動作。 此電子郵件包含目前行進時間和行經指定路線所需的額外時間。

1. 在條件的 [若為 true]  分支中，選取 [新增動作]  。

1. 在 [選擇動作]  底下，選取 [標準]  。 在搜尋方塊中，輸入「傳送電子郵件」。 此清單會傳回許多結果，因此請先選取您想要的電子郵件連接器，例如：

   ![選取您想要使用的電子郵件連接器](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

   * 對於 Azure 工作或學校帳戶，選取 [Office 365 Outlook]  。
   * 對於個人 Microsoft 帳戶，選取 [Outlook.com]  。

1. 當連接器的動作出現時，請選取您要使用的「傳送電子郵件動作」，例如：

   ![選取 [傳送電子郵件] 動作](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-send-email-action.png)

1. 如果您還沒有連線，系統會要求您登入您的電子郵件帳戶。

   Logic Apps 會建立與電子郵件帳戶的連線。

1. 以下列描述為動作重新命名：`Send email with travel time`

1. 在 [收件者]  方塊中，輸入收件者的電子郵件地址。 若要進行測試，請使用您的電子郵件地址。

1. 在 [主旨]  方塊中，指定電子郵件的主旨，以及包含 [travelTime]  變數。

   1. 輸入尾端有一個空格的文字 `Current travel time (minutes):`。 

   1. 在動態內容清單中，於 [變數]  底下選取 [查看更多]  。

      ![尋找 "travelTime" 變數](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-travelTime-variable.png)

   1. 在 [travelTime]  出現在 [變數]  底下後，選取 [travelTime]  。

      ![輸入主旨文字及傳回行進時間的運算式](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-travelTime-variable.png)

1. 在 [內文]  方塊中，指定電子郵件內文的內容。

   1. 輸入尾端有一個空格的文字 `Add extra travel time (minutes):`。

   1. 在動態內容清單中，選取 [運算式]  。

      ![建立電子郵件內文的運算式](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   1. 在運算式編輯器中，輸入此運算式，以便計算超過限制的分鐘數：```sub(,15)```

      ![輸入運算式來計算額外的行進時間分鐘數](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   1. 將游標放在運算式內部的左括號 ( **(** ) 與逗號 ( **,** ) 之間。 選取 [動態內容]  。

      ![繼續建立運算式來計算額外的行進時間分鐘數](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   1. 在 [變數]  下方，選取 [travelTime]  。

      ![選取要在運算式中使用的 "travelTime" 屬性](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   1. 在運算式內解析此屬性之後，請選取 [確定]  。

      ![在 [Body] 屬性解析之後，請選取 [確定]](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      [Body]  屬性隨即出現，如下所示：

      ![運算式中已解析的 [Body] 屬性](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

1. 儲存您的邏輯應用程式。

接著請測試您的邏輯應用程式，此時它看起來類似下列範例：

![完成的範例邏輯應用程式工作流程](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>執行邏輯應用程式

若要手動啟動邏輯應用程式，請在設計工具的工具列上選取 [執行]  。

* 如果目前行進時間保持在您的限制之內，邏輯應用程式並不會執行任何作業，而會等到下一個間隔後再次檢查。 

* 如果目前行進時間超過您的限制，您會收到一封電子郵件，其中包含目前行進時間和超過限制的分鐘數。 以下是邏輯應用程式傳送的範例電子郵件：

![顯示移動時間的已傳送電子郵件範例](./media/tutorial-build-scheduled-recurring-logic-app-workflow/received-example-email-notification.png)

若未收到任何電子郵件，請檢查電子郵件的垃圾郵件資料夾。 您的垃圾電子郵件篩選器可能會重新導向這類郵件。 或者，如果您不確定邏輯應用程式是否正確執行，請參閱[針對邏輯應用程式進行疑難排解](../logic-apps/logic-apps-diagnosing-failures.md)。

恭喜，您已經建立並執行以排程為基礎的週期性邏輯應用程式。 

若要建立使用 [週期]  觸發程序的其他邏輯應用程式，請簽出在您建立邏輯應用程式之後可用的下列範本：

* 讓每日提醒傳送給您。
* 刪除較舊的 Azure blob。
* 將訊息新增至 Azure 儲存體佇列。

## <a name="clean-up-resources"></a>清除資源

如果不再需要範例邏輯應用程式，請刪除包含邏輯應用程式的資源群組和相關資源。 

1. 在 Azure 主要功能表上，移至 [資源群組]  ，然後選取您邏輯應用程式的資源群組。

1. 在 [資源群組] 功能表上，選取 [概觀]   > [刪除資源群組]  。 

   ![[概觀] > [刪除資源群組]](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

1. 輸入資源群組名稱進行確認，然後選取 [刪除]  。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立一個邏輯應用程式，該邏輯應用程式可根據指定的排程 (在工作日上午) 檢查流量，並且在行進時間超過指定的限制時採取動作 (傳送電子郵件)。 現在，藉由 Azure 服務、Microsoft 服務和其他 SaaS 應用程式的整合，了解如何建立可傳送郵寄清單要求以供核准的邏輯應用程式。

> [!div class="nextstepaction"]
> [管理郵寄清單要求](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
