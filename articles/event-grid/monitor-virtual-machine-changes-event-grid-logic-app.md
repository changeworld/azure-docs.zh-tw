---
title: 監視虛擬機器變更 - Azure 事件方格和 Logic Apps
description: 使用 Azure 事件方格和 Logic Apps 來檢查虛擬機器 (VM) 的變更
services: logic-apps, event-grid
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: estfan, LADocs
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 7af555a634f0e362bdf2d530627a782843105bdf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87461267"
---
# <a name="tutorial-monitor-virtual-machine-changes-by-using-azure-event-grid-and-logic-apps"></a>教學課程：使用 Azure 事件方格和 Logic Apps 監視虛擬機器的變更

若要監視及回應發生在 Azure 資源或第三方資源中的特定事件，您可以建立程式碼使用量最低的[邏輯應用程式](../logic-apps/logic-apps-overview.md)，以工作流程的形式自動化及執行工作。 這些資源可以將事件發佈至 [Azure 事件方格](../event-grid/overview.md)。 接著，Event Grid 會將這些事件發送給以佇列、Webhook 或[事件中心](../event-hubs/event-hubs-about.md)作為端點的訂閱者。 身為訂閱者，邏輯應用程式可以先等待來自 Event Grid 的這些事件，再執行自動化工作流程來執行工作。

例如，以下是發行者可以透過 Azure Event Grid 服務傳送給訂閱者的一些事件：

* 建立、讀取、更新或刪除資源。 例如，您可以監視可能會產生 Azure 訂用帳戶費用並會影響帳單的變更。

* 從 Azure 訂用帳戶中新增或移除人員。

* 您的應用程式會執行特定動作。

* 新的訊息會出現在佇列中。

本教學課程會建立邏輯應用程式，可監視虛擬機器的變更，並傳送有關這些變更的電子郵件。 當您建立的邏輯應用程式具有 Azure 資源的事件訂用帳戶時，事件會透過 Event Grid 從該資源流向邏輯應用程式。 本教學課程會引導您建立此邏輯應用程式：

![Logic Apps 設計工具的螢幕擷取畫面，顯示使用事件方格監視 VM 的工作流程。](./media/monitor-virtual-machine-changes-event-grid-logic-app/monitor-virtual-machine-event-grid-logic-app-overview.png)

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立可從 Event Grid 監視事件的邏輯應用程式。
> * 新增可特別檢查虛擬機器變更的條件。
> * 在虛擬機器變更時傳送電子郵件。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 來自 Logic Apps 所支援電子郵件提供者的電子郵件帳戶，例如 Office 365 Outlook、Outlook.com 或 Gmail。 對於其他提供者，請[檢閱這裡的連接器清單](/connectors/)。

  本教學課程會使用 Office 365 Outlook 帳戶。 如果您使用不同的電子郵件帳戶，整體步驟將維持不變，但您的 UI 外觀可能會略有不同。

  > [!IMPORTANT]
  > 如果您想要使用 Gmail 連接器，只有 G-Suite 商務帳戶可以在邏輯應用程式中使用此連接器，而不受限制。 如果您有 Gmail 取用者帳戶，您只能使用此連接器搭配特定的 Google 核准服務，或者您可以[建立 Google 用戶端應用程式，以用來向 Gmail 連接器進行驗證](/connectors/gmail/#authentication-and-bring-your-own-application)。 如需詳細資訊，請參閱 [Azure Logic Apps 中 Google 連接器的資料安全性和隱私權原則](../connectors/connectors-google-data-security-privacy-policy.md)。

* 獨立於自己 Azure 資源群組中的[虛擬機器](https://azure.microsoft.com/services/virtual-machines)。 如果您尚未這樣做，請透過[建立 VM 教學課程](../virtual-machines/windows/quick-create-portal.md)來建立虛擬機器。 若要讓虛擬機器發佈事件，您[不需要執行任何其他作業](../event-grid/overview.md)。

## <a name="create-blank-logic-app"></a>建立空白邏輯應用程式

1. 使用您的 Azure 帳戶認證登入 [Azure 入口網站](https://portal.azure.com)。

1. 在主要 Azure 功能表中，選取 [建立資源]   > [整合]   > [邏輯應用程式]  。

   ![Azure 入口網站的螢幕擷取畫面，顯示用來建立邏輯應用程式資源的按鈕。](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

1. 在 [邏輯應用程式]  之下，提供邏輯應用程式資源的相關資訊。 當您完成時，選取 [建立]  。

   ![邏輯應用程式建立功能表的螢幕擷取畫面，顯示名稱、訂用帳戶、資源群組和位置等詳細資料。](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | 屬性 | 必要 | 值 | 描述 |
   |----------|----------|-------|-------------|
   | **名稱** | 是 | <*logic-app-name*> | 提供邏輯應用程式的唯一名稱。 |
   | **訂用帳戶** | 是 | <*Azure-subscription-name*> | 在本教學課程中，請針對所有服務選取相同的 Azure 訂用帳戶。 |
   | **資源群組** | 是 | <*Azure-resource-group*> | 邏輯應用程式的 Azure 資源組名稱，您可以針對本教學課程中的所有服務選取此名稱。 |
   | **位置** | 是 | <*Azure-region*> | 在本教學課程中，針對所有服務選取相同的區域。 |
   |||

1. 在 Azure 部署您的邏輯應用程式之後，Logic Apps 設計工具會顯示含有簡介影片和常用觸發程序的頁面。 將影片與觸發程序捲動過去。

1. 在 [範本]  底下，選取 [空白邏輯應用程式]  。

   ![Logic Apps 範本的螢幕擷取畫面，顯示用來建立空白邏輯應用程式的選取項目。](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   Logic Apps 設計工具現在會顯示[*觸發程序*](../logic-apps/logic-apps-overview.md#logic-app-concepts)，您可用於啟動邏輯應用程式。 每個邏輯應用程式都必須使用觸發程序啟動，而該觸發程序會在特定事件發生或符合特定條件時引發。 每次引發觸發程序時，Azure Logic Apps 會建立執行邏輯應用程式的工作流程執行個體。

## <a name="add-an-event-grid-trigger"></a>新增事件方格觸發程序

現在，新增事件方格觸發程序來監視您虛擬機器的資源群組。

1. 在設計工具的搜尋方塊中，輸入 `event grid` 作為篩選條件。 從觸發程序清單中選取 [當資源事件發生時]  觸發程序。

   ![Logic Apps 設計工具的螢幕擷取畫面，顯示資源事件中事件方格觸發程式的選取項目。](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

1. 出現提示時，使用您的 Azure 帳戶認證登入 Azure 事件方格。 在 [租用戶]  清單中 (其中顯示與您的 Azure 訂用帳戶相關聯的 Azure Active Directory 租用戶)，檢查正確的租用戶是否出現，例如：

   ![Logic Apps 設計工具的螢幕擷取畫面，顯示連線至事件方格的 Azure 登入提示。](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > 如果您已使用個人 Microsoft 帳戶登入，例如 @outlook.com 或 @hotmail.com，Event Grid 觸發程序可能無法正確顯示。 如需因應措施，請選取[與服務主體連線](../active-directory/develop/howto-create-service-principal-portal.md)，或以您與 Azure 訂用帳戶相關聯之 Azure Active Directory 的成員身分進行驗證，例如 user-name  @emailoutlook.onmicrosoft.com。

1. 現在以邏輯應用程式訂閱發行者的事件。 提供如下表中所述的事件訂閱詳細資料，例如：

   ![Logic Apps 設計工具的螢幕擷取畫面，顯示發生資源事件時的觸發程式詳細資料編輯器。](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

   | 屬性 | 必要 | 值 | 描述 |
   | -------- | -------- | ----- | ----------- |
   | **訂用帳戶** | 是 | <*event-publisher-Azure-subscription-name*> | 選取與「事件發行者」  相關聯的 Azure 訂用帳戶名稱。 在本教學課程中，選取虛擬機器的 Azure 訂用帳戶名稱。 |
   | **資源類型** | 是 | <*event-publisher-Azure-resource-type*> | 選取事件發行者的 Azure 資源類型。 如需有關 Azure 資源類型的詳細資訊，請參閱 [Azure 資源提供者和類型](../azure-resource-manager/management/resource-providers-and-types.md)。 在本教學課程中，選取 `Microsoft.Resources.ResourceGroups` 值以監視 Azure 資源群組。 |
   | **資源名稱** |  是 | <*event-publisher-Azure-resource-name*> | 選取事件發行者的 Azure 資源名稱。 這份清單會根據您選取的資源類型而有所不同。 在此教學課程中，請選取包含虛擬機器的 Azure 資源群組名稱。 |
   | **事件類型項目** |  否 | <*event-types*> | 選取要篩選的一或多個特定事件類型，並傳送至您的事件方格。 例如，您可以選擇性地新增這些事件類型，以偵測資源何時遭到變更或刪除： <p><p>- `Microsoft.Resources.ResourceActionSuccess` <br>- `Microsoft.Resources.ResourceDeleteSuccess` <br>- `Microsoft.Resources.ResourceWriteSuccess` <p>如需詳細資訊，請參閱下列主題： <p><p>- [Azure Event Grid 資源群組事件結構描述](../event-grid/event-schema-resource-groups.md) <br>- [了解事件篩選](../event-grid/event-filtering.md) <br>- [針對事件方格篩選事件](../event-grid/how-to-filter-events.md) |
   | 若要新增選擇性屬性，請選取 [新增參數]  ，然後選取您想要的屬性。 | 否 | {請參閱說明} | * **前置詞篩選**：在此教學課程中，將此屬性保留空白。 預設行為會比對所有的值。 不過，您可以指定前置詞字串作為篩選條件，例如，特定資源的路徑和參數。 <p>* **後置詞篩選**：在此教學課程中，將此屬性保留空白。 預設行為會比對所有的值。 不過，您可以指定前置詞字串作為篩選條件，例如，副檔名 (如果只想要特定檔案類型)。 <p>* **訂用帳戶名稱**：在此教學課程中，您可以為事件訂閱提供唯一名稱。 |
   |||

1. 儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]  。 若要在邏輯應用程式中摺疊並隱藏動作的詳細資料，請選取動作的標題列。

   ![Logic Apps 設計工具的螢幕擷取畫面，顯示儲存工作流程編輯的 [儲存] 按鈕。](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   當您儲存應用程式邏輯與 Event Grid 觸發程序時，Azure 會自動針對您的邏輯應用程式建立所選取資源的事件訂用帳戶。 因此當資源將事件發佈到 Event Grid 時，該 Event Grid 會將事件自動推送至邏輯應用程式。 此事件會觸發邏輯應用程式，然後建立並執行您在後續步驟中定義之工作流程的執行個體。

邏輯應用程式現在已上架，並且會接聽來自 Event Grid 的事件，但是在您將動作新增至工作流程以前，不會執行任何動作。

## <a name="add-a-condition"></a>新增條件

若只要在發生特定事件或作業時執行邏輯應用程式，請新增可檢查 `Microsoft.Compute/virtualMachines/write` 作業的條件。 當此條件為 true 時，應用程式邏輯會傳送電子郵件給您，其中包含已更新虛擬機器的詳細資料。

1. 在 Logic Apps 設計工具的事件方格觸發程序之下，選取 [新增步驟]  。

   ![Logic Apps 設計工具的螢幕擷取畫面，顯示用來將新步驟新增至工作流程的按鈕。](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-new-step-condition.png)

1. 在 [選擇動作]  底下的搜尋方塊中，輸入 `condition` 作為篩選條件。 從動作清單中，選取 [條件]  動作。

   ![Logic Apps 設計工具的螢幕擷取畫面，顯示用來新增條件動作的按鈕。](./media/monitor-virtual-machine-changes-event-grid-logic-app/select-condition.png)

   Logic Apps 設計工具會將空白條件新增至您的工作流程，包括根據條件為 true 或 false 所要遵循的動作路徑。

   ![Logic Apps 設計工具的螢幕擷取畫面，顯示已新增至工作流程的空白條件。](./media/monitor-virtual-machine-changes-event-grid-logic-app/empty-condition.png)

1. 將條件標題重新命名為 `If a virtual machine in your resource group has changed`。 在條件的標題列上，選取省略符號 ( **...** ) 按鈕，然後選取 [重新命名]  。

   ![Logic Apps 設計工具的螢幕擷取畫面，顯示條件編輯器的內容功能表中已選取 [重新命名] 選項。](./media/monitor-virtual-machine-changes-event-grid-logic-app/rename-condition.png)

1. 建立一個條件，以針對 `operationName` 屬性等於 `Microsoft.Compute/virtualMachines/write` 作業的 `data` 物件，檢查事件 `body`。 深入了解 [Event Grid 事件結構描述](../event-grid/event-schema.md)。

   1. 在第一個資料列的 [和]  下方，按一下左側方塊內部。 在顯示的動態內容清單中，選取 [運算式]  。

      ![Logic Apps 設計工具的螢幕擷取畫面，顯示已選取運算式編輯器的條件。](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-choose-expression.png)

   1. 在運算式編輯器中輸入此運算式，運算式會從觸發程序傳回作業名稱，然後選取 [確定]  ：

      `triggerBody()?['data']['operationName']`

      例如：

      ![Logic Apps 設計工具的螢幕擷取畫面，顯示使用運算式擷取作業名稱的條件編輯器。](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-add-data-operation-name.png)

   1. 在中間的方塊中，將運算子保留為 [等於]  。

   1. 在右側方塊中輸入此值，這是您要監視的特定作業：

      `Microsoft.Compute/virtualMachines/write`

   您完成的條件現在看起來就像下面這個範例︰

   ![Logic Apps 設計工具的螢幕擷取畫面，顯示比較作業的條件。](./media/monitor-virtual-machine-changes-event-grid-logic-app/complete-condition.png)

   如果您從設計檢視切換為程式碼檢視，而後回到設計檢視，您在條件中指定的運算式會解析為 **data.operationName** 語彙基元：

   ![Logic Apps 設計工具的螢幕擷取畫面，顯示具有已解析權杖的條件。](./media/monitor-virtual-machine-changes-event-grid-logic-app/resolved-condition.png)

1. 儲存您的邏輯應用程式。

## <a name="send-email-notifications"></a>傳送電子郵件通知

現在新增[動作  ](../logic-apps/logic-apps-overview.md#logic-app-concepts)，以在指定的條件為 true 時收到電子郵件。

1. 在條件的 [若為 true]  方塊中，選取 [新增動作]  。

   ![Logic Apps 設計工具條件編輯器的螢幕擷取畫面，顯示當條件為 True 時，用來新增動作的按鈕。](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-true-add-action.png)

1. 在 [選擇動作]  底下的搜尋方塊中，輸入 `send an email` 作為篩選條件。 根據您的電子郵件提供者，尋找並選取相符的連接器。 然後針對您的連接器選取 [傳送電子郵件] 動作。 例如：

   * 對於 Azure 公司或學校帳戶，選取 Office 365 Outlook 連接器。

   * 對於個人 Microsoft 帳戶，選取 Outlook.com 連接器。

   * 對於 Gmail 帳戶，選取 Gmail 連接器。

   本教學課程將使用 Office 365 Outlook 連接器繼續操作。 如果您使用不同的提供者，步驟維持不變，但您的 UI 可能會稍微不同。

   ![Logic Apps 設計工具的螢幕擷取畫面，顯示 Office 365 Outlook 連接器中的 [傳送電子郵件] 動作的搜尋。](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

1. 如果您還沒有電子郵件提供者的連線，請在系統要求您進行驗證時登入您的電子郵件帳戶。

1. 將傳送電子郵件動作重新命名為此標題：`Send email when virtual machine updated`

1. 如下表中所指定，提供電子郵件的相關資訊：

   ![Logic Apps 設計工具的螢幕擷取畫面，顯示針對 True 條件而新增至電子郵件主旨列的動態內容。](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > 若要從工作流程的先前步驟輸出中選取，請按一下編輯方塊以便顯示動態內容清單，或選取 [新增動態內容]  。 如需更多結果，在清單中的每個區段選取 [更多資訊]  。 若要關閉動態內容清單，請再次選取 [新增動態內容]  。

   | 屬性 | 必要 | 值 | 描述 |
   | -------- | -------- | ----- | ----------- |
   | **若要** | 是 | <*recipient\@domain*> | 輸入收件者的電子郵件地址。 為了測試用途，您可以使用自己的電子郵件地址。 |
   | **主旨** | 是 | `Resource updated:` **主旨** | 輸入電子郵件主旨的內容。 在本教學課程中，輸入指定的文字，然後選取事件的 [主旨]  欄位。 在這裡，您的電子郵件主旨包含更新資源 (虛擬機器) 的名稱。 |
   | **本文** | 是 | `Resource:` **主題** <p>`Event type:` **事件類型**<p>`Event ID:` **識別碼**<p>`Time:` **事件時間** | 輸入電子郵件內文的內容。 在本教學課程中，輸入指定的文字並選取事件的 [主題]  、[事件類型]  、[識別碼]  和 [事件時間]  欄位，讓您的電子郵件包含觸發事件的資源、事件類型、事件戳記，以及更新的事件識別碼。 在本教學課程中，此資源是觸發程序中選取的 Azure 資源群組。 <p>若要在內容中新增空白的行，請按 Shift + Enter。 |
   ||||

   > [!NOTE]
   > 如果您選取代表陣列的欄位，設計工具會自動新增以參考該陣列的動作為主的 **For each** 迴圈。 如此一來，應用程式邏輯會在每個陣列項目上執行該動作。

   現在，您的電子郵件動作看起來可能就像下面這個範例︰

   ![Logic Apps 設計工具的螢幕擷取畫面，顯示已選取要在 VM 更新時要在電子郵件中傳送的輸出。](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   完成的邏輯應用程式可能如此範例所示︰

   ![Logic Apps 設計工具的螢幕擷取畫面，顯示已建立的邏輯應用程式，以及觸發程式和動作的詳細資料。](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

1. 儲存您的邏輯應用程式。 若要在邏輯應用程式中摺疊並隱藏每個動作的詳細資料，請選取動作的標題列。

   邏輯應用程式現在已上限，但是在執行任何作業之前，等候您的虛擬機器變更。 若要立即測試應用程式邏輯，請繼續下一節。

## <a name="test-your-logic-app-workflow"></a>測試邏輯應用程式工作流程

1. 若要檢查邏輯應用程式會取得指定的事件，請更新您的虛擬機器。

   例如，您可以在 Azure 入口網站中調整您的虛擬機器大小，或[使用 Azure PowerShell 調整您的 VM 大小](../virtual-machines/windows/resize-vm.md)。

   一會兒之後，您應可取得電子郵件。 例如：

   ![範例 Outlook 電子郵件的螢幕擷取畫面，顯示有關 VM 更新的詳細資料。](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

1. 若要檢閱邏輯應用程式的執行和觸發歷程記錄，請在邏輯應用程式功能表上選取 [概觀]  。 若要檢視執行的更多詳細資料，請選取該執行的資料列。

   ![邏輯應用程式概觀頁面的螢幕擷取畫面，顯示已選取成功的執行。](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history.png)

1. 若要檢視每個步驟的輸入和輸出，請展開您需要檢閱的步驟。 此資訊可協助您診斷和偵錯應用程式邏輯中的問題。

   ![邏輯應用程式執行歷程記錄的螢幕擷取畫面，顯示每次執行的詳細資料。](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history-details.png)

恭喜，您已建立並執行邏輯應用程式，該邏輯應用程式可透過 Event Grid 監視資源事件，以及在這些事件發生時監視電子郵件。 您也了解如何輕鬆地建立工作流程，以自動執行程序並整合系統與雲端服務。

您可以使用事件格線和邏輯應用程式來監視其他組態變更，例如：

* 虛擬機器取得角色型存取控制 (RBAC) 權限。
* 對網路介面 (NIC) 上的網路安全性群組 (NSG) 進行變更。
* 已新增或移除虛擬機器的磁碟。
* 已將公用 IP 位址指派給虛擬機器 NIC。

## <a name="clean-up-resources"></a>清除資源

本教學課程使用會資源並執行會產生 Azure 訂用帳戶費用的動作。 當您完成本教學課程和測試時，請務必停用或刪除您不想產生費用的任何資源。

* 若要停止執行邏輯應用程式但是不刪除您的工作，請停用您的應用程式。 在邏輯應用程式功能表上，選取 [概觀]  。 在工具列上，選取 [停用]  。

  ![邏輯應用程式概觀的螢幕擷取畫面，顯示已選取停用按鈕以停用邏輯應用程式。](./media/monitor-virtual-machine-changes-event-grid-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > 如果您沒有看到邏輯應用程式功能表，請嘗試返回 Azure 儀表板，然後重新開啟邏輯應用程式。

* 若要永久刪除邏輯應用程式，請在邏輯應用程式功能表上選取 [概觀]  。 在工具列上，選取 [刪除]  。 確認您想要刪除邏輯應用程式，然後選取 [刪除]  。

## <a name="next-steps"></a>後續步驟

* [使用 Event Grid 建立和路由傳送自訂事件](../event-grid/custom-event-quickstart.md)
