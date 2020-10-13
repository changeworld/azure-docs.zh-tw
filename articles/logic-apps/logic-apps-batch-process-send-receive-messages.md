---
title: 以群組方式批次處理訊息
description: 使用 Azure Logic Apps 中的批次處理，在工作流程之間的群組中傳送和接收訊息
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: article
ms.date: 07/31/2020
ms.openlocfilehash: 0985afe3ddfd0d9de3c36ad6b030b6f259708c88
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87458202"
---
# <a name="send-receive-and-batch-process-messages-in-azure-logic-apps"></a>在 Azure Logic Apps 中傳送、接收及批次處理訊息

若要以群組的特定方式來傳送和處理訊息，您可以建立批次處理方案。 此解決方案會將訊息收集到 *批次* 中，並等到符合您指定的準則後，再釋出並處理批次訊息。 批次處理可降低您的邏輯應用程式處理訊息的頻率。

本文說明如何藉由在相同的 Azure 訂用帳戶、Azure 區域內建立兩個邏輯應用程式，並依照下列順序建立批次處理解決方案：

1. [「批次接收者」](#batch-receiver)邏輯應用程式，會接受訊息並收集到批次中，直到符合指定準則而可釋出和處理這些訊息。 請務必先建立此批次接收者，以便稍後在建立批次傳送者時選取批次目的地。

1. 一或多個[「批次傳送者」](#batch-sender)邏輯應用程式，可將訊息傳送給先前建立的批次接收者。

   您也可以指定唯一索引鍵，例如客戶編號，以便將目標批次「分割」** 或劃分為以該索引鍵為基礎的邏輯子集。 如此一來，接收者應用程式即可使用相同的索引鍵來收集所有項目並同時處理它們。

您的批次接收者和批次傳送者必須共用相同的 Azure 訂用帳戶 *和* azure 區域。 如果未共用，您就無法在建立批次傳送者時選取批次接收者，原因是兩者並無法看到彼此。

## <a name="prerequisites"></a>Prerequisites

* Azure 帳戶和訂用帳戶。 如果您沒有訂用帳戶，您可以[開始使用免費 Azure 帳戶](https://azure.microsoft.com/free/)。 或者，請[註冊隨用隨付訂用帳戶](https://azure.microsoft.com/pricing/purchase-options/)。

* 電子郵件帳戶與任何 [Azure Logic Apps 所支援的電子郵件提供者](../connectors/apis-list.md)

  > [!IMPORTANT]
  > 如果您想要使用 Gmail 連接器，只有 G-Suite 商務帳戶可以在邏輯應用程式中使用此連接器，而不受限制。 如果您有 Gmail 取用者帳戶，您只能使用此連接器搭配特定的 Google 核准服務，或者您可以[建立 Google 用戶端應用程式，以用來向 Gmail 連接器進行驗證](/connectors/gmail/#authentication-and-bring-your-own-application)。 如需詳細資訊，請參閱 [Azure Logic Apps 中 Google 連接器的資料安全性和隱私權原則](../connectors/connectors-google-data-security-privacy-policy.md)。

* [如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識

* 若要使用 Visual Studio 而不是 Azure 入口網站，請確定您已 [設定 Visual Studio 來使用 Logic Apps](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)。

<a name="batch-receiver"></a>

## <a name="create-batch-receiver"></a>建立批次接收者

若要能夠將訊息傳送到批次中，該批次必須先成為這些訊息的傳送目的地。 因此，您必須先建立「批次接收者」邏輯應用程式 (其會從**批次**觸發程序來開始)。 這樣一來，當您建立「批次傳送者」邏輯應用程式時，就可以選取批次接收者邏輯應用程式。 批次接收者會繼續收集訊息，直到符合指定準則而可釋出和處理這些訊息。 雖然批次接收者不需要知道批次傳送者的任何訊息，但批次傳送者必須知道訊息的傳送目的地。

1. 在 [Azure 入口網站](https://portal.azure.com) 或 Visual Studio 中，使用下列名稱建立邏輯應用程式： `BatchReceiver`

1. 在邏輯應用程式設計工具中，新增 **批次** 觸發程式，以啟動邏輯應用程式工作流程。 在搜尋方塊中，輸入 `batch` 並選取此觸發程式： **批次訊息**

   ![新增 [批次訊息] 觸發程序](./media/logic-apps-batch-process-send-receive-messages/add-batch-receiver-trigger.png)

1. 設定批次接收者的屬性：

   | 屬性 | 說明 |
   |----------|-------------|
   | **批次模式** | - **內嵌**：用來定義批次觸發程序內的發行準則 <br>- **整合帳戶**：透過[整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)，可定義多個發行準則組態。 透過整合帳戶，您可以在同一個地方維護上述所有組態，而不是在個別的邏輯應用程式中進行維護。 |
   | **批次名稱** | 您的批次名稱 (在此範例中為 "TestBatch")，只適用於 [內嵌]**** 批次模式 |
   | **發行準則** | 僅適用於 [內嵌]**** 批次模式，可選取在處理每個批次之前要先符合的準則： <p>- 以**訊息計數為基礎**：根據批次收集的訊息數目來釋放批次。 <br>- **大小**：根據該批次所收集之所有訊息的總大小（以位元組為單位）來釋放批次。 <br>- **排程**：根據週期排程釋出批次，以指定間隔和頻率。 在進階選項中，您也可以選取時區並提供開始日期和時間。 <br>- **全選**：使用所有指定的準則。 |
   | **訊息計數** | 要在批次中收集的訊息數目，例如 10 則訊息。 批次的限制為 8,000 則訊息。 |
   | **批次大小** | 要在批次中收集的大小總計 (以位元組為單)，例如 10 MB。 批次的大小限制為 80 MB。 |
   | **[排程]** | 批次發行之間的間隔和頻率，例如 10 分鐘。 最小週期是 60 秒或 1 分鐘。 小數的分鐘會無條件進位至 1 分鐘。 若要指定時區或開始日期和時間，請開啟 [ **加入新的參數** ] 清單，然後選取對應的屬性。 |
   |||

   > [!NOTE]
   >
   > 如果您在觸發程序仍有未傳送訊息的批次時變更發行準則，觸發程序就會使用更新後的發行準則來處理未傳送的訊息。

   此範例會顯示所有準則，但是供您自己測試，只嘗試一個準則：

   ![提供批次觸發程序詳細資料](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-criteria.png)

1. 現在新增可處理每個批次的一或多個動作。

   在此範例中，新增可在批次觸發程序引發時傳送電子郵件的動作。 當批次有 10 則訊息、達到 10 MB，或在經過 10 分鐘之後，觸發程序會執行並傳送一封電子郵件。

   1. 在批次觸發程式底下，選取 [ **新增步驟**]。

   1. 在搜尋方塊中，輸入 `send email` 作為篩選條件。 根據您的電子郵件提供者，選取電子郵件連接器。

      例如，如果您有公司或學校帳戶（例如 @fabrikam.com 或），請 @fabrikam.onmicrosoft.com 選取 **Microsoft 365 Outlook** 連接器。 如果您有個人帳戶（例如 @outlook.com 或），請 @hotmail.com 選取 **Outlook.com** 連接器。 此範例使用 Microsoft 365 Outlook 連接器。

   1. 為您的提供者選取 [傳送電子郵件] 動作，例如：

      ![為您的電子郵件提供者選取「傳送電子郵件」動作](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-send-email-action.png)

1. 如果出現提示，請登入您的電子郵件帳戶。

1. 設定所新增動作的屬性。

   * 在 [收件者]**** 方塊中，輸入收件者的電子郵件地址。 為了測試用途，您可以使用自己的電子郵件地址。

   * 在 [ **主旨** ] 方塊中，當動態內容清單出現時，請選取 [資料 **分割名稱** ] 欄位。

     ![從動態內容清單中，選取 [分割名稱]](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details.png)

     稍後在批次傳送者中，您可以指定唯一的分割索引鍵，將目標批次分成邏輯子集，以便可以傳送訊息至該處。 每個集合都有一個由批次傳送者邏輯應用程式產生的唯一數字。 這項功能可讓您使用具有多個子集的單一批次，並用您提供的名稱定義每個子集。

     > [!IMPORTANT]
     > 資料分割有 5,000 則訊息或 80 MB 的限制。 如果符合任何一個條件，Logic Apps 可以發行批次，即使不符合您定義的發行條件。

   * 在 [ **內** 文] 方塊中，當動態內容清單出現時，請選取 [ **訊息識別碼** ] 欄位。

     邏輯應用程式設計工具會自動在傳送電子郵件動作前後新增 **for each** 迴圈，因為該動作會將前一個動作的輸出視為集合，而不是批次。

     ![對於 [內文]，選取 [訊息識別碼]](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details-for-each.png)

1. 儲存您的邏輯應用程式。 您現在已建立批次接收者。

    ![儲存您的邏輯應用程式](./media/logic-apps-batch-process-send-receive-messages/save-batch-receiver-logic-app.png)

   > [!IMPORTANT]
   > 如果您使用 Visual Studio，在繼續下一節之前，請先確定您已先將[批次接收者邏輯應用程式*部署*到 Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure)。 否則，當您建立批次傳送者時，將無法選取批次接收者。

<a name="batch-sender"></a>

## <a name="create-batch-sender"></a>建立批次傳送者

現在，請建立一或多個會將訊息傳送給接收者邏輯應用程式的批次傳送者邏輯應用程式。 在每個批次傳送者中，您可以指定批次接收者和批次名稱、訊息內容，和任何其他設定。 您可以選擇性地提供唯一的分割索引鍵，將批次分成邏輯子集來收集具有該索引鍵的訊息。

* 確定您先前已 [建立並部署您的批次接收者](#batch-receiver) ，因此當您建立批次傳送者時，您可以選取現有的批次接收器作為目的地批次。 雖然批次接收者不需要知道批次傳送者的任何訊息，但批次傳送者必須知道訊息的傳送目的地。

* 請確定您的批次接收者和批次傳送者共用相同的 Azure 區域 *和* azure 訂用帳戶。 如果未共用，您就無法在建立批次傳送者時選取批次接收者，原因是兩者並無法看到彼此。

1. 使用下列名稱來建立另一個邏輯應用程式： `BatchSender`

   1. 在搜尋方塊中，輸入 `recurrence` 作為篩選條件。 從觸發程序清單中，選取此觸發程序：**週期性**

      ![新增週期觸發程序](./media/logic-apps-batch-process-send-receive-messages/add-schedule-trigger-batch-sender.png)

   1. 設定每分鐘執行傳送者邏輯應用程式的間隔和頻率。

      ![設定迴圈觸發程式的頻率和間隔](./media/logic-apps-batch-process-send-receive-messages/recurrence-trigger-batch-sender-details.png)

1. 新增動作來將訊息傳送至批次。

   1. 在 [ **週期** ] 觸發程式底下，選取 [ **新增步驟**]。

   1. 在搜尋方塊中，輸入 `batch` 做為篩選準則，然後選取此動作： **選擇具有批次觸發程式的 Logic Apps 工作流程**

      ![選取 [選擇包含批次觸發程序的 Logic Apps 工作流程]](./media/logic-apps-batch-process-send-receive-messages/send-messages-batch-action.png)

      清單隨即出現，而且只會顯示有批次觸發程式的邏輯應用程式，以及與您的 batch 傳送者邏輯應用程式位於相同 Azure 區域 *和* Azure 訂用帳戶的邏輯應用程式。

   1. 從邏輯應用程式清單中，選取您先前建立的批次接收者邏輯應用程式。

      ![選取您的批次接收者邏輯應用程式](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch-receiver.png)

      > [!IMPORTANT]
      > 如果您使用 Visual Studio，且沒有看到任何要選取的批次接收者，請確認您先前已建立批次接收者，並將其部署到 Azure。 如果您尚未這麼做，請瞭解 [如何將批次接收者邏輯應用程式部署到 Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure)。

   1. 從動作清單中，選取此動作： **Batch_messages-<*您的邏輯應用程式名稱* > **

      ![選取此動作："Batch_messages - <your-logic-app>"](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch.png)

1. 設定批次傳送者的屬性：

   | 屬性 | 說明 |
   |----------|-------------|
   | **批次名稱** | 接收者邏輯應用程式所定義的批次名稱， `TestBatch` 在此範例中為 <p>**重要**：批次名稱會在執行階段驗證，而且必須符合接收者邏輯應用程式所指定的名稱。 變更批次名稱會導致批次傳送者失敗。 |
   | **訊息內容** | 您要傳送的訊息內容 |
   |||

   > [!NOTE]
   > **觸發程式名稱**和**工作流程**屬性值會自動從您選取的邏輯應用程式填入。

   針對此範例，請新增此運算式，它會將目前日期和時間插入到您傳送給批次的訊息內容：

   1. 按一下 [訊息內容]**** 方塊內部。

   1. 當動態內容清單出現時，請選取 [ **運算式**]。

   1. 輸入運算式 `utcnow()` ，然後選取 **[確定]**。

      ![在 [訊息內容] 中，選取 [運算式]，輸入 "utcnow ( # A1"，然後選取 [確定]。](./media/logic-apps-batch-process-send-receive-messages/batch-sender-details.png)

1. 現在設定批次的資料分割。 在 [ `BatchReceiver` 動作] 中，開啟 [ **加入新的參數** ] 清單，然後選取下列屬性：

   | 屬性 | 說明 |
   |----------|-------------|
   | **分割區名稱** | 選擇性的唯一分割索引鍵，可用於將目標批次分割誠邏輯子集，以及根據該索引鍵收集訊息 |
   | **訊息識別碼** | 選擇性的訊息識別碼，空白時是一個產生的 全域唯一識別碼 (GUID) |
   |||

   針對此範例，在 [分割名稱]**** 中，請新增可產生介於 1 到 5 隨機數字的運算式。 讓 [訊息識別碼]**** 方塊保持空白。

   1. 在 [分割名稱]**** 方塊內按一下，動態內容清單隨即顯示。

   1. 在動態內容清單中，選取 [運算式]。

   1. 輸入運算式 `rand(1,6)` ，然後選取 **[確定]**。

      ![設定目標批次的資料分割](./media/logic-apps-batch-process-send-receive-messages/batch-sender-partition-advanced-options.png)

      這個 **rand** 函式會產生一到五之間的數字。 因此您會將此批次分割成五個有編號的資料分割，此運算式會以動態方式設定。

1. 儲存您的邏輯應用程式。 傳送者邏輯應用程式現在看起來應該類似這個範例：

   ![儲存您的傳送者邏輯應用程式](./media/logic-apps-batch-process-send-receive-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>儲存邏輯應用程式

若要測試批次處理解決方案，請讓邏輯應用程式執行幾分鐘的時間。 很快，您會開始收到以五封為單位的電子郵件群組，全都具有相同的資料分割索引鍵。

批次傳送者邏輯應用程式會每分鐘執行、產生介於 1 到 5 的隨機數字，並使用這個產生的數字為訊息傳送目標批次的分割索引鍵。 每次批次有五個項目具有相同的分割索引鍵時，批次接收者邏輯應用程式便會引發，並針對每個訊息傳送郵件。

> [!IMPORTANT]
> 當您完成測試時，請務必停用 `BatchSender` 邏輯應用程式，以停止傳送訊息並避免您的收件匣超載。

## <a name="next-steps"></a>接下來的步驟

* [批次處理並傳送 EDI 訊息](../logic-apps/logic-apps-scenario-edi-send-batch-messages.md)
* [使用 JSON 建置於邏輯應用程式定義上](../logic-apps/logic-apps-author-definitions.md)
* [在 Visual Studio 中使用 Azure Logic Apps 和 Functions 建置邏輯應用程式](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [適用於邏輯應用程式的例外狀況處理與記錄錯誤](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
