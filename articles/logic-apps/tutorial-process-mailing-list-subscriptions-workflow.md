---
title: 建置以核准為基礎的自動化工作流程
description: 教學課程 - 建立以核准為基礎的自動化工作流程，以使用 Azure Logic Apps 來處理郵寄清單的訂閱
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/07/2020
ms.openlocfilehash: 102b1946021aff7f8ab5491ed70fbc6cf772e3a8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91842108"
---
# <a name="tutorial-create-automated-approval-based-workflows-by-using-azure-logic-apps"></a>教學課程：使用 Azure Logic Apps 建立以核准為基礎的自動化工作流程

本教學課程會說明如何建立[邏輯應用程式](../logic-apps/logic-apps-overview.md)範例來自動化以核准為基礎的工作流程。 具體而言，此邏輯應用程式範例會處理 [MailChimp](https://mailchimp.com/) 服務所管理的郵寄清單訂閱要求。 此邏輯應用程式包含各種步驟，其一開始會監視要求的電子郵件帳戶、傳送這些要求以進行核准、檢查要求是否獲得核准、將核准的成員新增至郵寄清單，以及確認是否已將新成員新增至清單。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> * 建立空白邏輯應用程式。
> * 新增用來監視訂閱要求之電子郵件的觸發程序。
> * 新增可傳送電子郵件的動作，以便核准或拒絕這些要求。
> * 新增可檢查核准回覆的條件。
> * 新增可將已核准的成員新增至郵寄清單的動作。
> * 新增可檢查這些成員是否成功加入清單的條件。
> * 新增可傳送電子郵件的動作，以確認這些成員是否成功加入清單。

當您完成時，邏輯應用程式大致如下列工作流程所示︰

![完成的高階邏輯應用程式概觀](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-overview.png)

## <a name="prerequisites"></a>Prerequisites

* Azure 帳戶和訂用帳戶。 如果您沒有訂用帳戶，請[註冊一個免費的 Azure 帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 您先前在其中建立 "test-members-ML" 清單的 MailChimp 帳戶，您的邏輯應用程式可以在其中為已核准的成員新增電子郵件地址。 如果您沒有帳戶，請[註冊一個免費帳戶](https://login.mailchimp.com/signup/)並了解[如何建立 MailChimp 清單](https://us17.admin.mailchimp.com/lists/#)。

* Logic Apps 支援的任何電子郵件提供者 (例如 Office 365 Outlook、Outlook.com 或 Gmail) 所提供的電子郵件帳戶。 對於其他提供者，請[檢閱這裡的連接器清單](/connectors/)。 本快速入門會使用 Office 365 Outlook 搭配公司或學校帳戶。 如果您使用不同的電子郵件帳戶，整體步驟將維持不變，但您的 UI 可能略有不同。

* Office 365 Outlook 或 Outlook.com 中的電子郵件帳戶，可支援核准工作流程。 本教學課程是使用 Office 365 Outlook。 如果您使用不同的電子郵件帳戶，整體步驟將維持不變，但您的 UI 外觀可能會略有不同。

## <a name="create-your-logic-app"></a>建立邏輯應用程式

1. 使用您的 Azure 帳戶認證登入 [Azure 入口網站](https://portal.azure.com)。 在 Azure 首頁上，選取 [建立資源]。

1. 在 Azure Marketplace 功能表上，選取 [整合] > [邏輯應用程式]。

   ![此螢幕擷取畫面顯示已選取 [整合] 和 [邏輯應用程式] 的 Azure Marketplace 功能表。](./media/tutorial-process-mailing-list-subscriptions-workflow/create-new-logic-app-resource.png)

1. 在 [邏輯應用程式] 窗格上，提供這裡所述的資訊，其描述您想要建立的邏輯應用程式。

   ![此螢幕擷取畫面顯示邏輯應用程式建立窗格，以及要提供給新邏輯應用程式的資訊。](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | 屬性 | 值 | 描述 |
   |----------|-------|-------------|
   | **訂用帳戶** | <*Azure-subscription-name*> | 您的 Azure 訂用帳戶名稱。 此範例會使用 `Pay-As-You-Go`。 |
   | **資源群組** | LA-MailingList-RG | [Azure 資源群組](../azure-resource-manager/management/overview.md)的名稱，其用來組織相關資源。 此範例會建立新的資源群組，名為 `LA-MailingList-RG`。 |
   | **名稱** | LA-MailingList | 您的邏輯應用程式名稱，其中只能包含字母、數字、連字號 (`-`)、底線 (`_`)、括弧(`(`、`)`) 和句點 (`.`)。 此範例會使用 `LA-MailingList`。 |
   | **位置** | 美國西部 | 用來存放邏輯應用程式資訊的區域。 此範例會使用 `West US`。 |
   | **Log Analytics** | 關閉 | 保留診斷記錄的 [關閉] 設定。 |
   ||||

1. 當您完成時，選取 [檢閱 + 建立]。 在 Azure 驗證邏輯應用程式的相關資訊之後，請選取 [建立]。

1. 在 Azure 部署您的應用程式之後，請選取 [移至資源]。

   Azure 會開啟 Logic Apps 範本選取窗格，其中顯示簡介影片、常用的觸發程序，以及邏輯應用程式範本模式。

1. 向下捲動影片和觸發程序直到 [範本]區段，然後選取 [空白邏輯應用程式]。

   ![此螢幕擷取畫面顯示已選取 [空白邏輯應用程式] 的 Logic Apps 範本選取窗格。](./media/tutorial-process-mailing-list-subscriptions-workflow/select-logic-app-template.png)

接下來，請新增 Outlook [觸發程序](../logic-apps/logic-apps-overview.md#logic-app-concepts)，用以接聽含有訂閱要求的內送電子郵件。 每個邏輯應用程式都必須使用觸發程序啟動，而該觸發程序會在特定事件發生或新資料符合特定條件時引發。 如需詳細資訊，請參閱[建立您的第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="add-trigger-to-monitor-emails"></a>新增觸發程序來監視電子郵件

1. 在 [Logic Apps 設計工具] 搜尋方塊中，輸入 `when email arrives`，然後選取名為「當新電子郵件送達時 (When a new email arrives)」的觸發程序。

   * 對於 Azure 工作或學校帳戶，選取 [Office 365 Outlook]。
   * 對於個人 Microsoft 帳戶，選取 [Outlook.com]。

   此範例會選取 Office 365 Outlook 以繼續。

   ![顯示 Logic Apps 設計工具搜尋方塊的螢幕擷取畫面，其中包含 [當電子郵件送達時] 搜尋字詞，而 [當新電子郵件送達時] 觸發程序會顯示為已選取。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

1. 如果您還沒有連線，請在出現提示時登入並驗證電子郵件帳戶的存取權。

   Azure Logic Apps 會建立與電子郵件帳戶的連線。

1. 在觸發程序中，提供檢查新電子郵件的準則。

   1. 指定用來檢查電子郵件的資料夾，並將其他屬性設定保留為預設值。

      ![顯示 [當新電子郵件送達時] 動作和 [資料夾] 設定為 [收件匣] 的設計工具螢幕擷取畫面。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

   1. 新增觸發程序的**主旨篩選**屬性，讓您可以根據主旨行來篩選電子郵件。 開啟 [新增參數] 清單，然後選取 [主旨篩選]。

      ![此螢幕擷取畫面顯示已開啟的 [新增參數] 清單，並且已選取 [主旨篩選]。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-add-properties.png)

      如需此觸發程序屬性的詳細資訊，請參閱 [Office 365 Outlook 連接器參考](/connectors/office365/)或 [Outlook.com 連接器參考](/connectors/outlook/)。

   1. 當屬性出現在觸發程序之後，請輸入下列文字：`subscribe-test-members-ML`

      ![顯示 [主旨篩選器] 屬性並已輸入 "subscribe-test-members-ML" 文字的螢幕擷取畫面。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-subject-filter-property.png)

1. 若要立即隱藏觸發程序的詳細資料，請按一下圖形的標題列內部來摺疊圖形。

   ![此螢幕擷取畫面顯示已摺疊的觸發程序圖形。](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

1. 儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]。

邏輯應用程式目前啟用中，但是不會執行檢查內送電子郵以外的任何其他作業。 因此，新增會在觸發程序引發時回應的動作。

## <a name="send-approval-email"></a>傳送核准電子郵件

現在您有觸發程序，請新增可傳送電子郵件來核准或拒絕要求的[動作](../logic-apps/logic-apps-overview.md#logic-app-concepts)。

1. 在 Logic Apps 設計工具中，於 [當新電子郵件送達時] 觸發程序底下選取 [新增步驟]。

1. 在 [選擇作業] 底下的搜尋方塊中，輸入 `send approval`，然後選取名為 [傳送核准電子郵件] 的動作。

   ![此螢幕擷取畫面顯示依 [核准] 動作篩選的 [選擇作業] 清單，並且已選取 [傳送核准電子郵件] 動作。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

1. 現在輸入指定屬性的值，如下所示及所述。 將所有其他項目保留為預設值。 如需有關這些屬性的詳細資訊，請參閱 [Office 365 Outlook 連接器參考](/connectors/office365/)或 [Outlook.com 連接器參考](/connectors/outlook/)。

   ![顯示 [傳送核准電子郵件] 屬性的螢幕擷取畫面](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | 屬性 | 值 | 描述 |
   |----------|-------|-------------|
   | **若要** | <*approval-email-address*> | 核准者的電子郵件地址。 為了測試用途，您可以使用自己的地址。 此範例會使用虛構的 `sophiaowen@fabrikam.com` 電子郵件地址。 |
   | **主旨** | `Approve member request for test-members-ML` | 描述性電子郵件主旨 |
   | **使用者選項** | `Approve, Reject` | 請確定此屬性指定的是核准者可以選取的回應選項，預設為 [核准] 或 [拒絕]。 |
   ||||

   > [!NOTE]
   > 當您在某些編輯方塊內按一下時，動態內容清單會隨即出現，但您現在可以忽略此清單。 此清單會顯示先前動作的輸出，供您選取作為工作流程中後續動作的輸入。
 
1. 儲存您的邏輯應用程式。

接下來，新增一個條件以檢查核准者所選的回覆。

## <a name="check-approval-response"></a>檢查核准回覆

1. 在 [傳送核准電子郵件] 動作之下，選取 [新增步驟]。

1. 在 [選擇作業] 底下，選取 [內建]。 在搜尋方塊中，輸入 `condition`，然後選取名為 [條件] 的動作。

   ![顯示 [選擇作業] 搜尋方塊的螢幕擷取畫面，其中包已選取 [內建] 並以「條件」作為搜尋詞彙，而 [條件] 動作則顯示為已選取。](./media/tutorial-process-mailing-list-subscriptions-workflow/select-condition-action.png)

1. 在 [條件] 標題列上，選取**省略符號** (...) 按鈕，然後選取 [重新命名]。 以下列描述為條件重新命名：`If request approved`

   ![此螢幕擷取畫面顯示選取的省略符號按鈕與開啟的 [設定] 清單，以及選取的 [重新命名] 命令。](./media/tutorial-process-mailing-list-subscriptions-workflow/rename-condition-description.png)

1. 建立可檢查核准者是否已選取 [核准] 的條件。

   1. 按一下條件左側的 [選擇值] 方塊內部。

   1. 從顯示的動態內容清單中，選取 [傳送核准電子郵件] 底下的 [SelectedOption] 屬性。

      ![顯示動態內容清單的螢幕擷取畫面，其中 [傳送核准電子郵件] 區段的 [SelectedOption] 輸出顯示為已選取。](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   1. 在中間的比較方塊中，選取 [等於] 運算子。

   1. 在條件右側的 [選擇值] 方塊中，輸入此文字：`Approve`。

      當您完成時，條件看起來就像下面這個範例︰

      ![此螢幕擷取畫面顯示已核准要求範例的已完成條件](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

1. 儲存您的邏輯應用程式。

接下來，指定當檢閱者核准要求時邏輯應用程式執行的動作。 

## <a name="add-member-to-mailchimp-list"></a>將成員新增至 MailChimp 清單

現在，新增可將已核准的成員新增至郵寄清單的動作。

1. 在條件的 [True] 分支中，選取 [新增動作]。

1. 在 [選擇作業] 搜尋方塊底下，選取 [全部]。 在搜尋方塊中，輸入 `mailchimp`，然後選取名為 [將成員新增至清單] 的動作。

   ![此螢幕擷取畫面顯示 [選擇作業] 方塊及 [mailchimp] 搜尋詞彙，並已選取 [將成員新增至清單] 動作。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

1. 如果您尚未與 MailChimp 帳戶連線，系統會提示您登入。

1. 在 [將成員新增至清單] 動作中，提供如以下所示的資訊：

   ![顯示 [將成員新增至清單] 動作資訊的螢幕擷取畫面。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | 屬性 | 必要 | 值 | 說明 |
   |----------|----------|-------|-------------|
   | **清單識別碼** | 是 | <*mailing-list-name*> | 選取 MailChimp 郵寄清單的名稱。 此範例會使用 `test-members-ML`。 |
   | **電子郵件地址** | 是 | <new-member-email-address> | 在開啟的動態內容清單中，選取 [當新電子郵件送達時] 區段中的 [寄件者] (也就是觸發程序的輸出)，並為新成員指定電子郵件地址。 |
   | **狀態** | 是 | <*member-subscription-status*> | 選取要為新成員設定的訂閱狀態。 此範例會選取 `subscribed`。 <p>如需詳細資訊，請參閱[使用 MailChimp API 管理訂閱者](https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/)。 |
   |||||

   如需有關 [將成員新增至清單] 動作屬性的詳細資訊，請參閱 [MailChimp 連接器參考](/connectors/mailchimp/)。

1. 儲存您的邏輯應用程式。

接下來，新增條件，以便檢查新成員是否已成功加入郵寄清單。 如此一來，您的邏輯應用程式就可以通知您此作業已成功或失敗。

## <a name="check-for-success-or-failure"></a>檢查成功或失敗

1. 在 [True] 分支中，於 [將成員新增至清單] 動作之下，選取 [新增動作]。

1. 在 [選擇作業] 底下，選取 [內建]。 在搜尋方塊中，輸入 `condition`，然後選取名為 [條件] 的動作。

1. 以下列描述為條件重新命名：`If add member succeeded`

1. 建立可檢查已核准的成員加入郵寄清單成功或失敗的條件：

   1. 按一下條件左側的 [選擇值] 方塊內部。 從顯示的動態內容清單中，選取 [將成員新增至清單] 區段中的 [狀態] 屬性。

      例如，您的條件看起來就像下面這個範例︰

      ![顯示條件左側 [選擇值] 方塊並已輸入「狀態」的螢幕擷取畫面。](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   1. 在中間的比較方塊中，選取 [等於] 運算子。

   1. 在條件右側的 [選擇值] 方塊中，輸入此文字：`subscribed`

      當您完成時，條件看起來就像下面這個範例︰

      ![此螢幕擷取畫面顯示用來檢查訂閱成功或失敗的已完成條件。](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

接下來，設定要在已核准的成員加入郵寄清單成功或失敗時傳送的電子郵件。

## <a name="send-email-if-member-added"></a>如果已新增成員則傳送電子郵件

1. 在 [如果新增成員成功] 條件下方的 [True] 分支中，選擇 [新增動作]。

   ![此螢幕擷取畫面顯示 [如果新增成員成功] 條件的 [True] 分支，並已選取 [新增動作]。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

1. 在 [選擇作業] 搜尋方塊中，輸入 `outlook send email`，然後選取名為 [傳送電子郵件] 的動作。

   ![顯示「選擇作業」搜尋方塊的螢幕擷取畫面，其中已輸入「Outlook 傳送電子郵件」並已針對通知選取 [傳送電子郵件]。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

1. 以下列描述為動作重新命名：`Send email on success`

1. 在 [在成功時傳送電子郵件] 動作中，提供如以下所示的資訊：

   ![此螢幕擷取畫面顯示 [成功時傳送電子郵件] 動作和針對成功電子郵件提供的資訊。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | 屬性 | 必要 | 值 | 描述 |
   |----------|----------|-------|-------------|
   | **本文** | 是 | <*success-email-body*> | 成功電子郵件的內文內容。 在本教學課程中，請遵循下列步驟： <p>1.輸入此文字並在尾端加上空格：`New member has joined "test-members-ML":`。 <p>2.從顯示的動態內容清單中，選取 [電子郵件地址] 屬性。 <p>**注意**：如果未出現此屬性，請在 [將成員新增至清單] 區段標頭旁，選取 [查看更多]。 <p>3.在下一個資料列上，輸入此文字並在尾端加上空格：`Member opt-in status: ` <p>4.從動態內容清單中，選取 [將成員新增至清單] 下方的 [狀態] 屬性。 |
   | **主旨** | 是 | <*success-email-subject*> | 成功電子郵件的主旨。 在本教學課程中，請遵循下列步驟： <p>1.輸入此文字並在尾端加上空格：`Success! Member added to "test-members-ML": ` <p>2.從動態內容清單中，選取 [將成員新增至清單] 下方的 [電子郵件地址] 屬性。 |
   | **若要** | 是 | <your-email-address> | 要傳送成功電子郵件的電子郵件地址。 為了測試用途，您可以使用自己的電子郵件地址。 |
   |||||

1. 儲存您的邏輯應用程式。

## <a name="send-email-if-member-not-added"></a>如果未新增成員則傳送電子郵件

1. 在 [如果新增成員成功] 條件下方的 [False] 分支中，選擇 [新增動作]。

   ![此螢幕擷取畫面顯示 [如果新增成員成功] 條件的 [False] 分支，並已選取 [新增動作]。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

1. 在 [選擇作業] 搜尋方塊中，輸入 `outlook send email`，然後選取名為 [傳送電子郵件] 的動作。

   ![顯示「選擇作業」搜尋方塊的螢幕擷取畫面，其中已輸入「Outlook 傳送電子郵件」並已選取 [傳送電子郵件]。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

1. 以下列描述為動作重新命名：`Send email on failure`

1. 提供此動作的相關資訊，如下所示和描述：

   ![此螢幕擷取畫面顯示 [失敗時傳送電子郵件] 動作和針對失敗電子郵件提供的資訊。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | 屬性 | 必要 | 值 | 描述 |
   |----------|----------|-------|-------------|
   | **本文** | 是 | <body-for-failure-email> | 失敗電子郵件的內文內容。 在本教學課程中，請輸入此文字： <p>`Member might already exist. Check your MailChimp account.` |
   | **主旨** | 是 | <subject-for-failure-email> | 失敗電子郵件的主旨。 在本教學課程中，請遵循下列步驟： <p>1.輸入此文字並在尾端加上空格：`Failed, member not added to "test-members-ML": ` <p>2.從動態內容清單中，選取 [將成員新增至清單] 下方的 [電子郵件地址] 屬性。 |
   | **若要** | 是 | <your-email-address> | 要傳送失敗電子郵件的電子郵件地址。 為了測試用途，您可以使用自己的電子郵件地址。 |
   |||||

1. 儲存您的邏輯應用程式。 

接著請測試您的邏輯應用程式，此時它看起來類似下列範例：

![此螢幕擷取畫面顯示已完成的邏輯應用程式工作流程範例。](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-completed.png)

## <a name="run-your-logic-app"></a>執行邏輯應用程式

1. 將加入郵寄清單的電子郵件要求傳送給自己。 等候此要求出現在您的收件匣中。

1. 若要手動啟動邏輯應用程式，請在設計工具的工具列上選取 [執行]。 

   如果您的電子郵件主旨符合觸發程序的主旨篩選條件，則邏輯應用程式會傳送電子郵件給您，以核准訂閱要求。

1. 在您收到的核准電子郵件中，選取 [核准]。

1. 如果訂閱者的電子郵件地址不存在於郵寄清單，則邏輯應用程式會新增該人員的電子郵件地址，並將如下面範例所示的電子郵件傳送給您：

   ![此螢幕擷取畫面顯示成功訂閱的電子郵件範例。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-success.png)

   如果您的邏輯應用程式無法新增訂閱者，您會收到如下面範例所示的電子郵件：

   ![此螢幕擷取畫面顯示失敗訂閱的電子郵件範例。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-failed.png)

  > [!TIP]
  > 若未收到任何電子郵件，請檢查電子郵件的垃圾郵件資料夾。 您的垃圾電子郵件篩選器可能會重新導向這類郵件。 或者，如果您不確定邏輯應用程式是否正確執行，請參閱[針對邏輯應用程式進行疑難排解](../logic-apps/logic-apps-diagnosing-failures.md)。

恭喜，您現在已建立並執行可跨越 Azure、Microsoft 服務和其他 SaaS 應用程式整合資訊的邏輯應用程式。

## <a name="clean-up-resources"></a>清除資源

您的邏輯應用程式會繼續執行，直到您停用或刪除應用程式為止。 如果不再需要範例邏輯應用程式，請刪除包含邏輯應用程式的資源群組和相關資源。

1. 在 Azure 入口網站的搜尋方塊中，輸入您所建立的資源群組名稱。 從結果中，在 [資源群組] 底下，選取資源群組。

   此範例已建立名為 `LA-MailingList-RG` 的資源群組。

   ![此螢幕擷取畫面顯示已輸入 "la-mailinglist-rg" 並已選取 **LA-MailingList-RG** 的 Azure 搜尋方塊。](./media/tutorial-process-mailing-list-subscriptions-workflow/find-resource-group.png)

   > [!TIP]
   > 如果 Azure 首頁顯示 [最近的資源] 底下的資源群組，您可以從首頁中選取群組。

1. 在資源群組功能表上，檢查是否已選取 [概觀]。 在 [概觀] 窗格的工具列上，選取 [刪除資源群組]。

   ![此螢幕擷取畫面顯示資源群組的 [概觀] 窗格，並在窗格的工具列上選取了 [刪除資源群組]。](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

1. 在顯示的確認窗格中，輸入資源群組名稱，然後選取 [刪除]。

## <a name="next-steps"></a>後續步驟

在此教學課程中，您已建立可處理郵寄清單要求核准的邏輯應用程式。 現在，藉由 Azure 服務 (例如 Azure 儲存體和 Azure Functions) 的整合，了解如何建立可處理及儲存電子郵件附件的邏輯應用程式。

> [!div class="nextstepaction"]
> [處理電子郵件附件](../logic-apps/tutorial-process-email-attachments-workflow.md)
