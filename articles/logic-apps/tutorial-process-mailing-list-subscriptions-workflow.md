---
title: 建置以核准為基礎的自動化工作流程
description: 教學課程 - 建立以核准為基礎的自動化工作流程，以使用 Azure Logic Apps 來處理郵寄清單的訂閱
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/20/2019
ms.openlocfilehash: d9d2f29ffc34c203e5f3b3ebf094e73fb9cdfb75
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87132393"
---
# <a name="tutorial-create-automated-approval-based-workflows-by-using-azure-logic-apps"></a>教學課程：使用 Azure Logic Apps 建立以核准為基礎的自動化工作流程

本教學課程會說明如何建立[邏輯應用程式](../logic-apps/logic-apps-overview.md)來自動化以核准為基礎的工作流程。 具體而言，此邏輯應用程式會處理 [MailChimp](https://mailchimp.com/) 服務所管理的郵寄清單訂閱要求。 此邏輯應用程式會監視這些要求的電子郵件帳戶、傳送這些要求以供核准，並將已核准的成員新增至郵寄清單。

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

* Azure 訂用帳戶。 如果沒有訂用帳戶，請在開始前，先[註冊一個免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 包含 "test-members-ML" 清單的 MailChimp 帳戶，您的邏輯應用程式可以在其中為已核准的成員新增電子郵件地址。 如果您沒有帳戶，請[註冊一個免費帳戶](https://login.mailchimp.com/signup/)並了解[如何建立 MailChimp 清單](https://us17.admin.mailchimp.com/lists/#)。

* Office 365 Outlook 或 Outlook.com 中的電子郵件帳戶，可支援核准工作流程。 本文使用 Office 365 Outlook。 如果您使用不同的電子郵件帳戶，整體步驟將維持不變，但您的 UI 外觀可能會略有不同。

## <a name="create-your-logic-app"></a>建立邏輯應用程式

1. 使用您的 Azure 帳戶認證登入 [Azure 入口網站](https://portal.azure.com)。

1. 在主要 Azure 功能表中，選取 [建立資源] > [整合] > [邏輯應用程式]。

   ![建立您的新邏輯應用程式資源](./media/tutorial-process-mailing-list-subscriptions-workflow/create-new-logic-app-resource.png)

1. 在 [建立邏輯應用程式] 下，提供有關於邏輯應用程式的這項資訊，如下所示和描述。 當您完成時，選取 [建立]。

   ![提供邏輯應用程式的相關資訊](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | 屬性 | 值 | 說明 |
   |----------|-------|-------------|
   | **名稱** | LA-MailingList | 您的邏輯應用程式名稱，其中只能包含字母、數字、連字號 (`-`)、底線 (`_`)、括弧(`(`、`)`) 和句點 (`.`)。 此範例使用 "LA-MailingList"。 |
   | **訂用帳戶** | <your-Azure-subscription-name> | 您的 Azure 訂用帳戶名稱 |
   | **資源群組** | LA-MailingList-RG | [Azure 資源群組](../azure-resource-manager/management/overview.md)的名稱，其用來組織相關資源。 此範例使用 "LA-MailingList-RG"。 |
   | **位置** | 美國西部 | 用來存放邏輯應用程式資訊的區域。 此範例使用「美國西部」。 |
   | **Log Analytics** | 關閉 | 保留診斷記錄的 [關閉] 設定。 |
   ||||

1. 在 Azure 部署您的應用程式之後，請在 Azure 工具列上針對您部署的邏輯應用程式選取 [通知] > [移至資源]。

   ![移至您的新邏輯應用程式資源](./media/tutorial-process-mailing-list-subscriptions-workflow/go-to-logic-app-resource.png)

   或者，您可以在搜尋方塊中輸入名稱，以尋找並選取您的邏輯應用程式。

   Logic Apps 設計工具會開啟並顯示含有簡介影片和常用觸發程序和邏輯應用程式模式的頁面。 在 [範本] 底下，選取 [空白邏輯應用程式]。

   ![選取空白邏輯應用程式範本](./media/tutorial-process-mailing-list-subscriptions-workflow/select-logic-app-template.png)

接下來，請新增[觸發程序](../logic-apps/logic-apps-overview.md#logic-app-concepts)，用以接聽含有訂閱要求的內送電子郵件。 每個邏輯應用程式都必須使用觸發程序啟動，而該觸發程序會在特定事件發生或新資料符合特定條件時引發。 如需詳細資訊，請參閱[建立您的第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="add-trigger-to-monitor-emails"></a>新增觸發程序來監視電子郵件

1. 在 Logic Apps 設計工具的搜尋方塊中，輸入 `when email arrives` 作為篩選條件。 在 [觸發程序] 清單中，請為您的電子郵件提供者選取 [新的電子郵件送達時]。

   此範例使用 Office 365 Outlook 觸發程序：

   ![為電子郵件提供者選取 [新的電子郵件送達時] 觸發程序](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

   * 對於 Azure 工作或學校帳戶，選取 Office 365 Outlook。
   * 對於個人 Microsoft 帳戶，選取 Outlook.com。

1. 如果出現提示，請以您的認證登入電子郵件帳戶，讓 Logic Apps 能夠對您的電子郵件帳戶建立連線。

1. 在觸發程序中，提供檢查所有新電子郵件的準則。

   1. 指定檢查電子郵件的資料夾、間隔和頻率。

      ![指定檢查郵件的資料夾、間隔和頻率](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

      | 屬性 | 值 | 說明 |
      |----------|-------|-------------|
      | **資料夾** | `Inbox` | 要監視的電子郵件資料夾 |
      | **間隔** | `1` | 在檢查之間所要等待的間隔數目 |
      | **頻率** | `Hour` | 要用於週期的時間單位 |
      ||||

   1. 現在，將另一個屬性新增至觸發程序，以便您篩選電子郵件主旨行。 開啟 [新增參數清單]，然後選取 [主旨篩選] 屬性。

      ![將 [主旨篩選] 屬性加入至觸發程序](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-add-properties.png)

      如需此觸發程序屬性的詳細資訊，請參閱 [Office 365 Outlook 連接器參考](/connectors/office365/)或 [Outlook.com 連接器參考](/connectors/outlook/)。

   1. 當屬性出現在觸發程序之後，請輸入下列文字：`subscribe-test-members-ML`

      ![輸入 [主旨篩選] 屬性的文字](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-subject-filter-property.png)

1. 若要立即隱藏觸發程序的詳細資料，請按一下觸發程序的標題列。

   ![摺疊圖形以隱藏詳細資料](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

1. 儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]。

邏輯應用程式目前啟用中，但是不會執行檢查內送電子郵以外的任何其他作業。 因此，新增會在觸發程序引發時回應的動作。

## <a name="send-approval-email"></a>傳送核准電子郵件

現在您有觸發程序，請新增可傳送電子郵件來核准或拒絕要求的[動作](../logic-apps/logic-apps-overview.md#logic-app-concepts)。

1. 在觸發程序下方，選取 [新增步驟]。 

1. 在 [選擇動作] 底下的搜尋方塊中，輸入 `approval` 作為篩選條件。 在動作清單中，為您的電子郵件提供者選取 [傳送核准電子郵件] 動作。 

   此範例使用 Office 365 Outlook 動作：

   ![選取 [傳送核准電子郵件] 動作](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

1. 提供此動作的相關資訊，如下所述： 

   ![傳送核准電子郵件的屬性](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | 屬性 | 值 | 描述 |
   |----------|-------|-------------|
   | **若要** | <your-email-address> | 核准者的電子郵件地址。 為了測試用途，您可以使用自己的地址。 此範例會使用虛構的 "sophia.owen@fabrikam.com" 電子郵件地址。 |
   | **主旨** | `Approve member request for test-members-ML` | 描述性電子郵件主旨 |
   | **使用者選項** | `Approve, Reject` | 核准者可以選取的回應選項。 根據預設，核准者可以選取 [核准] 或 [拒絕] 作為其回應。 |
   ||||

   現在，請忽略當您在特定編輯方塊內按一下時所顯示的動態內容清單。 這份清單可讓您選取先前動作中的可用輸出，以作為工作流程中的輸入。

   如需此動作屬性的詳細資訊，請參閱 [Office 365 Outlook 連接器參考](/connectors/office365/)或 [Outlook.com 連接器參考](/connectors/outlook/)。
 
1. 儲存您的邏輯應用程式。

接下來，新增一個條件以檢查核准者所選的回覆。

## <a name="check-approval-response"></a>檢查核准回覆

1. 在 [傳送核准電子郵件] 動作之下，選取 [新增步驟]。

1. 在 [選擇動作] 底下，選取 [內建]。 在搜尋方塊中，輸入 `condition` 作為篩選條件。 從動作清單中，選取 [條件] 動作。

   ![尋找並選取 [條件] 動作](./media/tutorial-process-mailing-list-subscriptions-workflow/select-condition-action.png)

1. 以更適當的描述為條件重新命名。

   1. 在條件的標題列上，選取 **省略符號** ( **...** ) 按鈕 > [重新命名]。

      ![重新命名條件的描述](./media/tutorial-process-mailing-list-subscriptions-workflow/rename-condition-description.png)

   1. 以下列描述為條件重新命名：`If request approved`

1. 建立可檢查核准者是否已選取 [核准] 的條件。

   1. 在條件內，按一下條件左側的 [選擇值] 方塊內部。

   1. 從顯示的動態內容清單中，選取 [傳送核准電子郵件] 底下的 [SelectedOption] 屬性。

      ![從動態內容清單中，選取 [SelectedOption]](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   1. 在中間的比較方塊中，選取 [等於] 運算子。

   1. 在條件右側的 [選擇值] 方塊中，輸入此文字：`Approve`

      當您完成時，條件看起來就像下面這個範例︰

      ![完成的已核准條件範例](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

1. 儲存您的邏輯應用程式。

接下來，指定當檢閱者核准要求時邏輯應用程式執行的動作。 

## <a name="add-member-to-mailchimp-list"></a>將成員新增至 MailChimp 清單

現在，新增可將已核准的成員新增至郵寄清單的動作。

1. 在條件的 [若為 true] 分支中，選取 [新增動作]。

1. 在 [選擇動作] 底下，輸入 `mailchimp` 作為篩選條件，然後選取 [將成員新增至清單] 動作。

   ![選取 [將成員新增至清單] 動作](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

1. 如果系統提示您存取您的 MailChimp 帳戶，請以您的 MailChimp 認證登入。

1. 提供此動作的相關資訊，如下所示和描述：

   ![提供 [將成員新增至清單] 的資訊](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | 屬性 | 必要 | 值 | 說明 |
   |----------|----------|-------|-------------|
   | **清單識別碼** | 是 | `test-members-ML` | MailChimp 郵寄清單的名稱。 此範例會使用 "test-members-ML"。 |
   | **狀態** | 是 | `subscribed` | 選取新成員的訂閱狀態。 此範例使用 [已訂閱]。 <p>如需詳細資訊，請參閱[使用 MailChimp API 管理訂閱者](https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/)。 |
   | **電子郵件地址** | 是 | <new-member-email-address> | 從動態內容清單中，選取 [新的電子郵件送達時] 下方的 [寄件者]，以傳入新成員的電子郵件地址。 |
   ||||

   如需有關此動作屬性的詳細資訊，請參閱 [MailChimp 連接器參考](/connectors/mailchimp/)。

1. 儲存您的邏輯應用程式。

接下來，新增條件，以便檢查新成員是否已成功加入郵寄清單。 如此一來，邏輯應用程式會通知您這項作業成功或失敗。

## <a name="check-for-success-or-failure"></a>檢查成功或失敗

1. 在 [若為 true] 分支中，於 [將成員新增至清單] 動作之下，選取 [新增動作]。

1. 在 [選擇動作] 底下，選取 [內建]。 在搜尋方塊中，輸入 `condition` 作為篩選條件。 從動作清單中，選取 [條件]。

1. 以下列描述為條件重新命名：`If add member succeeded`

1. 建立可檢查已核准的成員加入郵寄清單成功或失敗的條件：

   1. 在條件內，按一下條件左側的 [選擇值] 方塊內部。 從動態內容清單中，選取 [將成員新增至清單] 下方的 [狀態] 屬性。

      例如，您的條件看起來就像下面這個範例︰

      ![在 [將成員新增至清單] 下選取 [狀態]](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   1. 在中間的比較方塊中，選取 [等於] 運算子。

   1. 在條件右側的 [選擇值] 方塊中，輸入此文字：`subscribed`

      當您完成時，條件看起來就像下面這個範例︰

      ![完成的已訂閱條件範例](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

接下來，設定要在已核准的成員加入郵寄清單成功或失敗時傳送的電子郵件。

## <a name="send-email-if-member-added"></a>如果已新增成員則傳送電子郵件

1. 在 [如果新增成員成功] 條件下方的 [若為 true] 分支中，選擇 [新增動作]。

   ![在 [若為 true] 分支中，選取 [新增動作]](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

1. 在 [選擇動作] 底下的搜尋方塊中，輸入 `outlook send email` 作為篩選條件，然後選取 [傳送電子郵件] 動作。

   ![新增 [傳送電子郵件] 動作](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

1. 以下列描述為動作重新命名：`Send email on success`

1. 提供此動作的資訊，如下所示和描述：

   ![提供成功電子郵件的資訊](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | 屬性 | 必要 | 值 | 描述 |
   |----------|----------|-------|-------------|
   | **若要** | 是 | <your-email-address> | 要傳送成功電子郵件的電子郵件地址。 為了測試用途，您可以使用自己的電子郵件地址。 |
   | **主旨** | 是 | <subject-for-success-email> | 成功電子郵件的主旨。 在本教學課程中，請輸入此文字： <p>`Success! Member added to "test-members-ML": ` <p>從動態內容清單中，選取 [將成員新增至清單] 下方的 [電子郵件地址] 屬性。 |
   | **本文** | 是 | <body-for-success-email> | 成功電子郵件的內文內容。 在本教學課程中，請輸入此文字： <p>`New member has joined "test-members-ML":` <p>從動態內容清單中，選取 [電子郵件地址] 屬性。 <p>在下一個資料列中，輸入下列文字：`Member opt-in status: ` <p> 從動態內容清單中，選取 [將成員新增至清單] 下方的 [狀態] 屬性。 |
   |||||

1. 儲存您的邏輯應用程式。

## <a name="send-email-if-member-not-added"></a>如果未新增成員則傳送電子郵件

1. 在 [如果新增成員成功] 條件下方的 [若為 false] 分支中，選擇 [新增動作]。

   ![在 [若為 false] 分支中，選取 [新增動作]](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

1. 在 [選擇動作] 底下的搜尋方塊中，輸入 `outlook send email` 作為篩選條件，然後選取 [傳送電子郵件] 動作。

   ![新增 [傳送電子郵件] 動作](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

1. 以下列描述為動作重新命名：`Send email on failure`

1. 提供此動作的相關資訊，如下所示和描述：

   ![提供失敗電子郵件的資訊](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | 屬性 | 必要 | 值 | 描述 |
   |----------|----------|-------|-------------|
   | **若要** | 是 | <your-email-address> | 要傳送失敗電子郵件的電子郵件地址。 為了測試用途，您可以使用自己的電子郵件地址。 |
   | **主旨** | 是 | <subject-for-failure-email> | 失敗電子郵件的主旨。 在本教學課程中，請輸入此文字： <p>`Failed, member not added to "test-members-ML": ` <p>從動態內容清單中，選取 [將成員新增至清單] 下方的 [電子郵件地址] 屬性。 |
   | **本文** | 是 | <body-for-failure-email> | 失敗電子郵件的內文內容。 在本教學課程中，請輸入此文字： <p>`Member might already exist. Check your MailChimp account.` |
   |||||

1. 儲存您的邏輯應用程式。 

接著請測試您的邏輯應用程式，此時它看起來類似下列範例：

![完成的邏輯應用程式工作流程範例](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-completed.png)

## <a name="run-your-logic-app"></a>執行邏輯應用程式

1. 將加入郵寄清單的電子郵件要求傳送給自己。 等候此要求出現在您的收件匣中。

1. 若要手動啟動邏輯應用程式，請在設計工具的工具列上選取 [執行]。 

   如果您的電子郵件主旨符合觸發程序的主旨篩選條件，則邏輯應用程式會傳送電子郵件給您，以核准訂閱要求。

1. 在核准電子郵件中，選取 [核准]。

1. 如果訂閱者的電子郵件地址不存在於郵寄清單，則邏輯應用程式會新增該人員的電子郵件地址，並將如下面範例所示的電子郵件傳送給您：

   ![範例電子郵件 - 訂閱成功](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-success.png)

   如果您的邏輯應用程式無法新增訂閱者，您會收到如下面範例所示的電子郵件：

   ![範例電子郵件 - 訂閱失敗](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-failed.png)

   若未收到任何電子郵件，請檢查電子郵件的垃圾郵件資料夾。 您的垃圾電子郵件篩選器可能會重新導向這類郵件。 或者，如果您不確定邏輯應用程式是否正確執行，請參閱[針對邏輯應用程式進行疑難排解](../logic-apps/logic-apps-diagnosing-failures.md)。

恭喜，您現在已建立並執行可跨越 Azure、Microsoft 服務和其他 SaaS 應用程式整合資訊的邏輯應用程式。

## <a name="clean-up-resources"></a>清除資源

如果不再需要範例邏輯應用程式，請刪除包含邏輯應用程式的資源群組和相關資源。 

1. 在 Azure 主要功能表上，移至 [資源群組]，然後選取您邏輯應用程式的資源群組。

1. 在 [資源群組] 功能表上，選取 [概觀] > [刪除資源群組]。 

   ![[概觀] > [刪除資源群組]](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

1. 輸入資源群組名稱進行確認，然後選取 [刪除]。

## <a name="next-steps"></a>後續步驟

在此教學課程中，您已建立可管理郵寄清單要求核准的邏輯應用程式。 現在，藉由 Azure 服務 (例如 Azure 儲存體和 Azure Functions) 的整合，了解如何建立可處理及儲存電子郵件附件的邏輯應用程式。

> [!div class="nextstepaction"]
> [處理電子郵件附件](../logic-apps/tutorial-process-email-attachments-workflow.md)
