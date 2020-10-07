---
title: 使用多項 Azure 服務自動執行工作
description: 教學課程 - 使用 Azure Logic Apps、Azure 儲存體和 Azure Functions 建立處理電子郵件的自動化工作流程
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 02/27/2020
ms.openlocfilehash: 38b4713383368f0c64983738f2ed65f60edb9e67
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91334084"
---
# <a name="tutorial-automate-tasks-to-process-emails-by-using-azure-logic-apps-azure-functions-and-azure-storage"></a>教學課程：使用 Azure Logic Apps、Azure Functions 和 Azure 儲存體，將工作自動化以處理電子郵件

Azure Logic Apps 可協助您自動執行工作流程，並整合 Azure 服務、Microsoft 服務和其他軟體即服務 (SaaS) 應用程式與內部部署系統的資料。 此教學課程說明如何建置[邏輯應用程式](../logic-apps/logic-apps-overview.md)，用以處理內送電子郵件和任何附件。 此邏輯應用程式可分析電子郵件內容、將內容儲存至 Azure 儲存體，以及傳送檢閱該內容的通知。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 設定 [Azure 儲存體](../storage/common/storage-introduction.md)和儲存體總管，用以檢查已儲存的電子郵件和附件。
> * 建立從電子郵件中移除 HTML 的 [Azure 函式](../azure-functions/functions-overview.md)。 此教學課程包含可供您用於此函式的程式碼。
> * 建立空白邏輯應用程式。
> * 新增用來監視電子郵件附件的觸發程序。
> * 新增會檢查電子郵件是否有附件的條件。
> * 新增會在電子郵件有附件時呼叫 Azure 函式的動作。
> * 新增會為電子郵件和附件建立儲存體 Blob 的動作。
> * 新增傳送電子郵件通知的動作。

當您完成時，邏輯應用程式大致如下列工作流程所示︰

![完成的邏輯應用程式概觀](./media/tutorial-process-email-attachments-workflow/overview.png)

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* Logic Apps 支援的任何電子郵件提供者 (例如 Office 365 Outlook、Outlook.com 或 Gmail) 所提供的電子郵件帳戶。 對於其他提供者，請[檢閱這裡的連接器清單](/connectors/)。

  此邏輯應用程式會使用公司或學校帳戶。 如果您使用不同的電子郵件帳戶，整體步驟將維持不變，但您的 UI 外觀可能會略有不同。

  > [!IMPORTANT]
  > 如果您想要使用 Gmail 連接器，只有 G-Suite 商務帳戶可以在邏輯應用程式中使用此連接器，而不受限制。 如果您有 Gmail 取用者帳戶，您只能使用此連接器搭配特定的 Google 核准服務，或者您可以[建立 Google 用戶端應用程式，以用來向 Gmail 連接器進行驗證](/connectors/gmail/#authentication-and-bring-your-own-application)。 如需詳細資訊，請參閱 [Azure Logic Apps 中 Google 連接器的資料安全性和隱私權原則](../connectors/connectors-google-data-security-privacy-policy.md)。

* 下載並安裝[免費 Microsoft Azure 儲存體總管](https://storageexplorer.com/)。 此工具可協助您確認儲存體容器已正確設定。

## <a name="set-up-storage-to-save-attachments"></a>設定用來儲存附件的儲存體

您可以將內送電子郵件和附件儲存為 [Azure 儲存體容器](../storage/common/storage-introduction.md)中的 Blob。

1. 使用您的 Azure 帳戶認證登入 [Azure 入口網站](https://portal.azure.com)。

1. 請先在 Azure 入口網站中的 [基本資料] 索引標籤上使用這些設定[建立儲存體帳戶](../storage/common/storage-account-create.md)，然後才能建立儲存體容器：

   | 設定 | 值 | 說明 |
   |---------|-------|-------------|
   | **訂用帳戶** | <*Azure-subscription-name*> | Azure 訂用帳戶的名稱 |  
   | **資源群組** | <*Azure-resource-group*> | 用來組織及管理相關資源的 [Azure 資源群組](../azure-resource-manager/management/overview.md)的名稱。 此範例使用 "LA-Tutorial-RG"。 <p>**注意：** 資源群組會存在於某個特定區域內。 雖然此教學課程中提及的項目可能並非適用於所有區域，但請盡可能使用相同的區域。 |
   | **儲存體帳戶名稱** | <*Azure-storage-account-name*> | 您的儲存體帳戶名稱，必須有 3-24 個字元，且只能包含小寫字母和數字。 此範例使用 "attachmentstorageacct"。 |
   | **位置** | <*Azure-region*> | 用來儲存您儲存體帳戶相關資訊的區域。 此範例使用「美國西部」。 |
   | **效能** | 標準 | 此設定會指定支援的資料類型和用來儲存資料的媒體。 請參閱[儲存體帳戶類型](../storage/common/storage-introduction.md#types-of-storage-accounts)。 |
   | **帳戶類型** | 一般用途 | [儲存體帳戶類型](../storage/common/storage-introduction.md#types-of-storage-accounts) |
   | **複寫** | 本機備援儲存體 (LRS) | 此設定會指定如何複製、儲存、管理及同步處理您的資料。 請參閱[本地備援儲存體 (LRS)：適用於 Azure 儲存體的低成本資料備援](../storage/common/storage-redundancy.md)。 |
   | **存取層 (預設值)** | 保留目前設定。 |
   ||||

   在 [進階] 索引標籤上，選取此設定：

   | 設定 | 值 | 說明 |
   |---------|-------|-------------|
   | **需要安全傳輸** | 已停用 | 此設定會指定連線要求所需的安全性。 請參閱[需要安全傳輸](../storage/common/storage-require-secure-transfer.md)。 |
   ||||

   若要建立儲存體帳戶，您也可以使用 [Azure PowerShell](../storage/common/storage-account-create.md?tabs=powershell) 或 [Azure CLI](../storage/common/storage-account-create.md?tabs=azure-cli)。

1. 當您完成時，選取 [檢閱 + 建立]。

1. 在 Azure 部署您的儲存體帳戶之後，請找出您的儲存體帳戶，並取得儲存體帳戶的存取金鑰：

   1. 在儲存體帳戶功能表的 [設定] 下，選取 [存取金鑰]。

   1. 複製您的儲存體帳戶名稱和 **key1**，並將這些值儲存到其他安全之處。

      ![複製並儲存儲存體帳戶名稱和金鑰](./media/tutorial-process-email-attachments-workflow/copy-save-storage-name-key.png)

   若要取得儲存體帳戶的存取金鑰，您也可以使用 [Azure PowerShell](/powershell/module/az.storage/get-azstorageaccountkey) 或 [Azure CLI](/cli/azure/storage/account/keys?view=azure-cli-latest.md#az-storage-account-keys-list)。

1. 建立電子郵件附件的 Blob 儲存體容器。

   1. 在您的儲存體帳戶功能表上，選取 [概觀]。 在 [概觀] 窗格上，選取 [容器]。

      ![新增 Blob 儲存體容器](./media/tutorial-process-email-attachments-workflow/create-storage-container.png)

   1. 在 [容器] 頁面開啟後，在工具列上選取 [容器]。

   1. 在 [新增容器] 下方，輸入 `attachments` 做為容器名稱。 在 [公用存取層級] 下選取 [容器 (容器和 Blob 匿名讀取權限)] > [確定]。

      完成作業後，您可以在 Azure 入口網站中的儲存體帳戶中找到您的儲存體容器，位置如下：

      ![已完成的儲存體容器](./media/tutorial-process-email-attachments-workflow/created-storage-container.png)

   若要建立儲存體容器，您也可以使用 [Azure PowerShell](/powershell/module/az.storage/new-azstoragecontainer) 或 [Azure CLI](/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create)。

接著，將儲存體總管連線至您的儲存體帳戶。

## <a name="set-up-storage-explorer"></a>設定儲存體總管

現在，將儲存體總管連線至儲存體帳戶，以確認邏輯應用程式可將附件正確地儲存為儲存體容器中的 Blob。

1. 啟動 Microsoft Azure 儲存體總管。

   儲存體總管會提示您連線至儲存體帳戶。

1. 在 [連線至 Azure 儲存體] 窗格中，選取 [使用儲存體帳戶名稱和金鑰] > [下一步]。

   ![儲存體總管 - 連線至儲存體帳戶](./media/tutorial-process-email-attachments-workflow/storage-explorer-choose-storage-account.png)

   > [!TIP]
   > 若未出現提示，請在儲存體總管工具列上選取 [新增帳戶]。

1. 在 [顯示名稱] 下方，提供連線的自訂名稱。 在 [帳戶名稱]  下方，提供您的儲存體帳戶名稱。 在 [帳戶金鑰] 下方，提供您先前儲存的存取金鑰，並選取 [下一步]。

1. 確認您的連線資訊，然後選取 [連線]。

   儲存體總管會建立連線，並在 [總管] 視窗中的 [Local & Attached] (本機與已連結的資源) > [儲存體帳戶] 下顯示您的儲存體帳戶。

1. 若要尋找您的 Blob 儲存體容器，請在 [儲存體帳戶] 下方展開您的儲存體帳戶 (在本文中為 **attachmentstorageacct**)，並展開 [附件] 容器所在的 [Blob 容器]，例如：

   ![儲存體總管 - 尋找儲存體容器](./media/tutorial-process-email-attachments-workflow/storage-explorer-check-contianer.png)

接著，建立從內送電子郵件中移除 HTML 的 [Azure 函式](../azure-functions/functions-overview.md)。

## <a name="create-function-to-clean-html"></a>建立會清除 HTML 的函式

現在，使用這些步驟所提供的程式碼片段，建立會從每個內送電子郵件中移除 HTML 的 Azure 函式。 這樣一來，電子郵件內容將更為簡潔而易於處理。 然後，您就可以從邏輯應用程式呼叫此函式。

1. 您必須先透過下列設定[建立函式應用程式](../azure-functions/functions-create-function-app-portal.md)，才能建立函式：

   | 設定 | 值 | 描述 |
   | ------- | ----- | ----------- |
   | **應用程式名稱** | <*function-app-name*> | 在整個 Azure 中，您的函數應用程式名稱必須是全域唯一的。 此範例已使用「CleanTextFunctionApp」，因此請提供不同的名稱，例如 "MyCleanTextFunctionApp-<*您的名稱*>" |
   | **訂用帳戶** | <your-Azure-subscription-name> | 您先前使用的相同 Azure 訂用帳戶 |
   | **資源群組** | LA-Tutorial-RG | 您先前使用的相同 Azure 資源群組 |
   | **作業系統** | <*your-operating-system*> | 選取支援您慣用函數程式設計語言的作業系統。 在此範例中，請選取 [Windows]。 |
   | **主控方案** | 取用方案 | 此設定會決定如何配置和調整執行函式應用程式所需的資源，例如運算能力。 請參閱[主控方案比較](../azure-functions/functions-scale.md)。 |
   | **位置** | 美國西部 | 您先前使用的相同區域 |
   | **執行階段堆疊** | 慣用語言 | 選取支援您慣用函式程式設計語言的執行階段。 針對 C# 和 F# 函式選取 **.NET**。 |
   | **Storage** | cleantextfunctionstorageacct | 為您的函式應用程式建立儲存體帳戶。 請一律使用小寫字母和數字。 <p>**注意：** 此儲存體帳戶包含您的函數應用程式，且不同於您先前為電子郵件附件建立的儲存體帳戶。 |
   | **Application Insights** | 停用 | 可開啟 [Application Insights](../azure-monitor/app/app-insights-overview.md) 的應用程式監視功能，但在此教學課程中，請選取 [停用] > [套用]。 |
   ||||

   如果函數應用程式未在部署之後自動開啟，請在 [Azure 入口網站](https://portal.azure.com) 搜尋方塊中尋找並選取 [函數應用程式]。 在 [函數應用程式] 下，選取您的函數應用程式。

   ![選取函數應用程式](./media/tutorial-process-email-attachments-workflow/select-function-app.png)

   否則，Azure 會自動開啟您的函式應用程式，如下所示：

   ![已建立的函式應用程式](./media/tutorial-process-email-attachments-workflow/function-app-created.png)

   若要建立函數應用程式，您也可以使用 [Azure CLI](../azure-functions/functions-create-first-azure-function-azure-cli.md)，或使用 [PowerShell 和 Resource Manager 範本](../azure-resource-manager/templates/deploy-powershell.md)。

1. 在 [函數應用程式] 清單中展開該函數應用程式 (如果尚未展開)。 在您的函數應用程式下，選取 [函數]。 在函式工具列上，選取 [+ 新增函式]。

   ![建立新的函式](./media/tutorial-process-email-attachments-workflow/function-app-new-function.png)

1. 在 [選擇下列範本或移至快速入門] 下，選取 [HTTP 觸發程序] 函式範本。

   ![選取 HTTP 觸發程序範本](./media/tutorial-process-email-attachments-workflow/function-select-httptrigger-csharp-function-template.png)

   Azure 會使用 HTTP 所觸發函式的特定語言範本來建立函式。

1. 在 [新增函式] 窗格中的 [名稱] 下方，輸入 `RemoveHTMLFunction`。 讓 [授權等級] 設定為 [函數]，然後選取 [建立]。

   ![函式命名](./media/tutorial-process-email-attachments-workflow/function-provide-name.png)

1. 編輯器開啟之後，請將範本程式碼取代為此範例程式碼，以移除 HTML 並將結果傳回至呼叫端：

   ```csharp
   #r "Newtonsoft.Json"

   using System.Net;
   using Microsoft.AspNetCore.Mvc;
   using Microsoft.Extensions.Primitives;
   using Newtonsoft.Json;
   using System.Text.RegularExpressions;

   public static async Task<IActionResult> Run(HttpRequest req, ILogger log) {

      log.LogInformation("HttpWebhook triggered");

      // Parse query parameter
      string emailBodyContent = await new StreamReader(req.Body).ReadToEndAsync();

      // Replace HTML with other characters
      string updatedBody = Regex.Replace(emailBodyContent, "<.*?>", string.Empty);
      updatedBody = updatedBody.Replace("\\r\\n", " ");
      updatedBody = updatedBody.Replace(@"&nbsp;", " ");

      // Return cleaned text
      return (ActionResult)new OkObjectResult(new { updatedBody });
   }
   ```

1. 完成時，選取 [儲存]。 若要測試您的函數，請在編輯器右邊緣的箭號 ( **<** ) 圖示下方選取 [測試]。

   ![開啟 [測試] 窗格](./media/tutorial-process-email-attachments-workflow/function-choose-test.png)

1. 在 [測試] 窗格的 [要求本文] 下輸入這一行，然後選取 [執行]。

   `{"name": "<p><p>Testing my function</br></p></p>"}`

   ![測試您的函式](./media/tutorial-process-email-attachments-workflow/function-run-test.png)

   [輸出] 視窗會顯示函式的結果：

   ```json
   {"updatedBody":"{\"name\": \"Testing my function\"}"}
   ```

確認您的函式運作正常後，請建立邏輯應用程式。 雖然本教學課程說明的是如何建立從電子郵件中移除 HTML 的函式，但 Logic Apps 也提供 **HTML 轉換為文字**的連接器。

## <a name="create-your-logic-app"></a>建立邏輯應用程式

1. 在 Azure 頂層搜尋方塊中輸入 `logic apps`，然後選取 [Logic Apps]。

   ![尋找並選取「邏輯應用程式」](./media/tutorial-process-email-attachments-workflow/find-select-logic-apps.png)

1. 在 [邏輯應用程式] 窗格上，選取 [新增]。

   ![新增邏輯應用程式](./media/tutorial-process-email-attachments-workflow/add-new-logic-app.png)

1. 在 [邏輯應用程式] 窗格上，提供邏輯應用程式的詳細資訊，如下所示。 在完成作業後，選取 [檢閱 + 建立]。

   ![提供邏輯應用程式資訊](./media/tutorial-process-email-attachments-workflow/create-logic-app-settings.png)

   | 設定 | 值 | 描述 |
   | ------- | ----- | ----------- |
   | **訂用帳戶** | <your-Azure-subscription-name> | 您先前使用的相同 Azure 訂用帳戶 |
   | **資源群組** | LA-Tutorial-RG | 您先前使用的相同 Azure 資源群組 |
   | **邏輯應用程式名稱** | LA-ProcessAttachment | 邏輯應用程式的名稱 |
   | **選取位置** | 美國西部 | 您先前使用的相同區域 |
   | **Log Analytics** | 關閉 | 在本教學課程中，請選取 [關閉] 設定。 |
   ||||

1. 在 Azure 部署您的應用程式之後，請在 Azure 工具列上選取通知圖示，並選取 [前往資源]。

   ![從 Azure 通知清單中，選取 [前往資源]](./media/tutorial-process-email-attachments-workflow/go-to-new-logic-app-resource.png)

1. Logic Apps 設計工具會隨即開啟，並顯示含有簡介影片和常用邏輯應用程式模式範本的頁面。 在 [範本] 底下，選取 [空白邏輯應用程式]。

   ![選取空白邏輯應用程式範本](./media/tutorial-process-email-attachments-workflow/choose-logic-app-template.png)

接下來，請新增[觸發程序](../logic-apps/logic-apps-overview.md#logic-app-concepts)，用以接聽含有附件的內送電子郵件。 每個邏輯應用程式都必須使用觸發程序啟動，而該觸發程序會在特定事件發生或新資料符合特定條件時引發。 如需詳細資訊，請參閱[建立您的第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="monitor-incoming-email"></a>監視內送電子郵件

1. 在設計工具的搜尋方塊中，輸入 `when new email arrives` 做為篩選條件。 為電子郵件提供者選取此觸發程序：**新的電子郵件送達時 - <*your-email-provider*>**

   例如：

   ![為電子郵件提供者選取此觸發程序：「新的電子郵件送達時」](./media/tutorial-process-email-attachments-workflow/add-trigger-when-email-arrives.png)

   * 對於 Azure 工作或學校帳戶，選取 Office 365 Outlook。

   * 對於個人 Microsoft 帳戶，選取 Outlook.com。

1. 如果系統要求提供認證，請登入您的電子郵件帳戶，讓 Logic Apps 能夠連線至您的電子郵件帳戶。

1. 現在，請提供觸發程序用來篩選新電子郵件的準則。

   1. 指定下述電子郵件檢查設定。

      ![指定檢查郵件的資料夾、間隔和頻率](./media/tutorial-process-email-attachments-workflow/set-up-email-trigger.png)

      | 設定 | 值 | 說明 |
      | ------- | ----- | ----------- |
      | **資料夾** | Inbox | 要檢查的電子郵件資料夾 |
      | **具有附件** | 是 | 僅取得含附件的電子郵件。 <p>**注意：** 觸發程序並不會從您的帳戶移除任何電子郵件，而只會檢查新的訊息及處理符合主旨篩選條件的電子郵件。 |
      | **包含附件** | 是 | 取得附件並作為工作流程的輸入，而非只是檢查是否有附件。 |
      | **間隔** | 1 | 在檢查之間所要等待的間隔數目 |
      | **頻率** | Minute | 在檢查之間每個間隔的時間單位 |
      ||||

   1. 從 [新增參數] 清單中，選取 [主旨篩選]。

   1. 在動作中出現 [主旨篩選] 方塊後，請指定如下所列的主體：

      | 設定 | 值 | 說明 |
      | ------- | ----- | ----------- |
      | **主旨篩選** | `Business Analyst 2 #423501` | 要在電子郵件主旨中尋找的文字 |
      ||||

1. 若要立即隱藏觸發程序的詳細資料，請按一下觸發程序的標題列內部。

   ![摺疊圖形以隱藏詳細資料](./media/tutorial-process-email-attachments-workflow/collapse-trigger-shape.png)

1. 儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]。

   邏輯應用程式目前作用中，但不會執行檢查電子郵件以外的任何其他作業。 接下來請新增條件，以指定繼續執行工作流程的準則。

## <a name="check-for-attachments"></a>檢查是否有附件

現在，請新增只會選取含附件之電子郵件的條件。

1. 在觸發程序下方，選取 [新增步驟]。

   ![[新增步驟]](./media/tutorial-process-email-attachments-workflow/add-condition-under-trigger.png)

1. 在 [選擇動作] 底下的搜尋方塊中，輸入 `condition`。 選取此動作：**Condition**

   ![選取 [條件]](./media/tutorial-process-email-attachments-workflow/select-condition.png)

   1. 以更適當的描述為條件重新命名。 在條件的標題列上，選取 省略符號 ( **...** ) 按鈕 > [重新命名]。

      ![重新命名條件](./media/tutorial-process-email-attachments-workflow/condition-rename.png)

   1. 以下列描述為條件重新命名：`If email has attachments and key subject phrase`

1. 建立會檢查電子郵件是否有附件的條件。

   1. 在第一個資料列的 [和] 下方，按一下左側方塊內部。 從顯示的動態內容清單中，選取 [有附件] 屬性。

      ![此螢幕擷取畫面顯示條件的 "And" 屬性，以及 [具有附件] 屬性選項。](./media/tutorial-process-email-attachments-workflow/build-condition.png)

   1. 在中間的方塊中，將運算子保留為 [等於]。

   1. 在右側的方塊中輸入 **True** 值，以與觸發程序中的 [有附件] 屬性值進行比較。

      ![建置條件](./media/tutorial-process-email-attachments-workflow/finished-condition.png)

      如果這兩個值相等，表示電子郵件至少有一個附件，因此符合條件，工作流程會繼續執行。

   在您可以使用程式碼編輯器視窗來檢視的基礎邏輯應用程式定義中，此條件會如下列範例所示：

   ```json
   "Condition": {
      "actions": { <actions-to-run-when-condition-passes> },
      "expression": {
         "and": [ {
            "equals": [
               "@triggerBody()?['HasAttachment']",
                 "true"
            ]
         } ]
      },
      "runAfter": {},
      "type": "If"
   }
   ```

1. 儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]。

### <a name="test-your-condition"></a>測試您的條件

現在，請測試條件是否正常運作：

1. 如果您的邏輯應用程式尚未執行，請在設計工具工具列上選取 [執行]。

   此步驟可讓您手動啟動邏輯應用程式，而不必等待指定的間隔經過。 不過，在測試電子郵件送達收件匣之前，並不會執行任何動作。

1. 將符合此準則的電子郵件傳送給自己：

   * 您的電子郵件主旨具有您在觸發程序的 [主旨篩選條件] 中指定的文字：`Business Analyst 2 #423501`

   * 您的電子郵件有一個附件。 現在，請建立一個空的文字檔，並將該檔案附加到您的電子郵件。

   當電子郵件送達時，邏輯應用程式會檢查是否有附件和指定的主旨文字。 如果符合條件，觸發程序即會引發，並使 Logic Apps 引擎建立邏輯應用程式執行個體及啟動工作流程。

1. 若要確認觸發程序已引發且邏輯應用程式已成功執行，請在邏輯應用程式功能表上選取 [概觀]。

   ![檢查觸發程序和執行歷程記錄](./media/tutorial-process-email-attachments-workflow/checkpoint-run-history.png)

   如果邏輯應用程式未觸發，或雖然成功觸發但未執行，請參閱[對邏輯應用程式進行疑難排解](../logic-apps/logic-apps-diagnosing-failures.md)。

接著，請定義要為 [若為 true] 分支採取的動作。 若要儲存電子郵件及任何附件，請從電子郵件內文中移除任何 HTML，然後在儲存體容器中為電子郵件與附件建立 Blob。

> [!NOTE]
> 電子郵件沒有附件時，邏輯應用程式不需要為 [若為 false] 分支執行任何動作。
> 如果您在完成此教學課程後想要做額外練習，您可以新增任何要為 [若為 false] 分支執行的適當動作。

## <a name="call-removehtmlfunction"></a>呼叫 RemoveHTMLFunction

此步驟會將您先前建立的 Azure 函式新增至邏輯應用程式，並將電子郵件觸發程序中的電子郵件內文內容傳至您的函式。

1. 在邏輯應用程式功能表上，選取 [邏輯應用程式設計工具]。 在 [若為 true] 分支中，選取 [新增動作]。

   ![在 [若為 true] 內，新增動作](./media/tutorial-process-email-attachments-workflow/if-true-add-action.png)

1. 在搜尋方塊中尋找「Azure 函式」，並選取下列動作：**選擇 Azure 函式 – Azure Functions**

   ![選取「選擇 Azure 函式」的動作](./media/tutorial-process-email-attachments-workflow/add-action-azure-function.png)

1. 選取您先前建立的函數應用程式，也就是此範例中的 `CleanTextFunctionApp`：

   ![選取您的 Azure 函式應用程式](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function-app.png)

1. 現在選取您的函式：**RemoveHTMLFunction**

   ![選取您的 Azure 函式](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function.png)

1. 以下列說明為函式圖形重新命名：`Call RemoveHTMLFunction to clean email body`

1. 現在，指定您的函式要處理的輸入。

   1. 在 [要求本文] 下方，輸入此文字並加上尾端空格：

      `{ "emailBody":`

      當您在後續步驟中處理此輸入時，在您的輸入正確地格式化為 JSON 之前，都會出現 JSON 無效的相關錯誤。 在您先前測試此函式時，為此函式指定的輸入使用的是 JavaScript 物件標記法 (JSON)。 因此，要求本文也必須使用相同的格式。

      此外，當游標位於 [要求本文] 方塊內時，也會出現動態內容清單，讓您可從先前的動作選取可用的屬性值。

   1. 在動態內容清單中的 [新的電子郵件送達時] 下方，選取 [內文] 屬性。 在此屬性後面，請記得加上右大括號：`}`

      ![指定要傳至函式的要求本文](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing.png)

   完成上述步驟後，函式的輸入會如下列範例所示︰

   ![已完成而要傳至函式的要求本文](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing-2.png)

1. 儲存您的邏輯應用程式。

接著，新增會在您的儲存體容器中建立 Blob 的動作，以便您儲存電子郵件內文。

## <a name="create-blob-for-email-body"></a>為電子郵件內文建立 Blob

1. 在 [若為 true] 區塊中，從您的 Azure 函數下方選取 [新增動作]。

1. 在搜尋方塊中，輸入 `create blob` 做為篩選條件，然後選取以下動作：**建立 Blob**

   ![新增為電子郵件內文建立 Blob 的動作](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-email-body.png)

1. 透過以下說明的設定建立儲存體帳戶的連線。 當您完成時，選取 [建立]。

   ![建立儲存體帳戶的連線](./media/tutorial-process-email-attachments-workflow/create-storage-account-connection-first.png)

   | 設定 | 值 | 描述 |
   | ------- | ----- | ----------- |
   | **連接名稱** | AttachmentStorageConnection | 連線的描述性名稱 |
   | **儲存體帳戶** | attachmentstorageacct | 您先前建立用來儲存附件之儲存體帳戶的名稱 |
   ||||

1. 以下列說明為 [建立 Blob] 動作重新命名：`Create blob for email body`

1. 在 [建立 Blob] 動作中提供此資訊並選取這些欄位，以建立 Blob，說明如下：

   ![提供電子郵件內文的 Blob 資訊](./media/tutorial-process-email-attachments-workflow/create-blob-for-email-body.png)

   | 設定 | 值 | 說明 |
   | ------- | ----- | ----------- |
   | **資料夾路徑** | /attachments | 您先前建立之容器的路徑和名稱。 在此範例中，請按一下資料夾圖示，然後選取 "/attachments" 容器。 |
   | **Blob 名稱** | **寄件者**欄位 | 此範例中，請使用寄件者的名稱作為 Blob 的名稱。 按一下此方塊內部讓動態內容清單顯示，然後在 [新的電子郵件送達時] 動作下方選取 [寄件者] 欄位。 |
   | **Blob 內容** | **內容**欄位 | 在此範例中，請使用無 HTML 電子郵件內文作為 Blob 內容。 按一下此方塊內部讓動態內容清單顯示，然後在 [呼叫 RemoveHTMLFunction 以清除電子郵件內文] 動作下方選取 [內文]。 |
   ||||

   完成上述步驟後，動作會如下列範例所示︰

   ![此螢幕擷取畫面顯示已完成「建立 Blob」動作的範例。](./media/tutorial-process-email-attachments-workflow/create-blob-for-email-body-done.png)

1. 儲存您的邏輯應用程式。

### <a name="check-attachment-handling"></a>檢查附件處理情形

現在，請測試邏輯應用程式是否依照您指定的方式處理電子郵件：

1. 如果您的邏輯應用程式尚未執行，請在設計工具工具列上選取 [執行]。

1. 將符合此準則的電子郵件傳送給自己：

   * 您的電子郵件主旨具有您在觸發程序的 [主旨篩選條件] 中指定的文字：`Business Analyst 2 #423501`

   * 您的電子郵件至少有一個附件。 現在，請建立一個空的文字檔，並將該檔案附加到您的電子郵件。

   * 您的電子郵件在內文中有一些測試內容，例如：`Testing my logic app`

   如果邏輯應用程式未觸發，或雖然成功觸發但未執行，請參閱[對邏輯應用程式進行疑難排解](../logic-apps/logic-apps-diagnosing-failures.md)。

1. 確認邏輯應用程式已將電子郵件儲存至正確的儲存體容器。

   1. 在儲存體總管中，依序展開 [Local & Attached] (本機與已連結的資源)  > [儲存體帳戶] > [attachmentstorageacct (金鑰)] > [Blob 容器] > [附件]。

   1. 檢查**附件**容器中是否有電子郵件。

      此時，只有電子郵件會出現在容器中，因為邏輯應用程式尚未處理附件。

      ![檢查儲存體總管中是否有已儲存的電子郵件](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-email.png)

   1. 完成作業後，請刪除儲存體總管中的電子郵件。

1. (選擇性) 若要測試 [若為 false] 分支 (至此尚未執行任何動作)，您可以傳送不符合準則的電子郵件。

接下來，請新增迴圈以處理所有電子郵件附件。

## <a name="process-attachments"></a>處理附件

若要處理電子郵件中的每個附件，請在邏輯應用程式的工作流程中新增 **For each** 迴圈。

1. 在 [為電子郵件內文建立 Blob] 圖形下方，選取 [新增動作]。

   ![新增 "for each" 迴圈](./media/tutorial-process-email-attachments-workflow/add-for-each-loop.png)

1. 在 [選擇動作] 底下的搜尋方塊中，輸入 `for each` 做為篩選條件，然後選取以下動作：**For each**

   ![選取 [For each]](./media/tutorial-process-email-attachments-workflow/select-for-each.png)

1. 以下列說明為迴圈重新命名：`For each email attachment`

1. 現在，指定要由迴圈處理的資料。 按一下 [選取先前步驟中的輸出] 方塊內部，讓動態內容清單開啟，然後選取 [附件]。

   ![取得「附件」](./media/tutorial-process-email-attachments-workflow/select-attachments.png)

   [附件] 欄位會傳入一個陣列，其中包含電子郵件所含的所有附件。 **For each** 迴圈會對隨陣列傳入的每個項目重複執行動作。

1. 儲存您的邏輯應用程式。

接著，請新增會在您的**附件**儲存體容器中將每個附件儲存為 Blob 的動作。

## <a name="create-blob-for-each-attachment"></a>為附件建立 Blob

1. 在 [For each email attachment] 迴圈中選取 [新增動作]，如此就能對每個找到的附件指定執行工作。

   ![將動作新增至迴圈](./media/tutorial-process-email-attachments-workflow/for-each-add-action.png)

1. 在搜尋方塊中，輸入 `create blob` 做為篩選條件，然後選取以下動作：**建立 Blob**

   ![新增建立 Blob 的動作](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-attachments.png)

1. 以下列說明為**建立 Blob 2** 動作重新命名：`Create blob for each email attachment`

1. 在 [為每個電子郵件附件建立 Blob] 動作中提供此資訊，並為您要建立的每個 Blob 選取屬性，說明如下：

   ![提供 Blob 資訊](./media/tutorial-process-email-attachments-workflow/create-blob-per-attachment.png)

   | 設定 | 值 | 說明 |
   | ------- | ----- | ----------- |
   | **資料夾路徑** | /attachments | 您先前建立之容器的路徑和名稱。 在此範例中，請按一下資料夾圖示，然後選取 "/attachments" 容器。 |
   | **Blob 名稱** | **名稱**欄位 | 此範例中，請使用附件的名稱作為 Blob 的名稱。 按一下此方塊內部讓動態內容清單顯示，然後在 [新的電子郵件送達時] 動作下方選取 [名稱] 欄位。 |
   | **Blob 內容** | **內容**欄位 | 在此範例中，請使用 [內容] 欄位作為 Blob 內容。 按一下此方塊內部讓動態內容清單顯示，然後在 [新的電子郵件送達時] 動作下方選取 [內容]。 |
   ||||

   完成上述步驟後，動作會如下列範例所示︰

   ![已完成的「建立 Blob」動作](./media/tutorial-process-email-attachments-workflow/create-blob-per-attachment-done.png)

1. 儲存您的邏輯應用程式。

### <a name="check-attachment-handling"></a>檢查附件處理情形

接著，請測試邏輯應用程式是否依照您指定的方式處理附件：

1. 如果您的邏輯應用程式尚未執行，請在設計工具工具列上選取 [執行]。

1. 將符合此準則的電子郵件傳送給自己：

   * 您的電子郵件主旨具有您在觸發程序的 [主旨篩選條件] 屬性中指定的文字：`Business Analyst 2 #423501`

   * 您的電子郵件至少有兩個附件。 現在，請建立兩個空的文字檔，並將這些檔案附加到您的電子郵件。

   如果邏輯應用程式未觸發，或雖然成功觸發但未執行，請參閱[對邏輯應用程式進行疑難排解](../logic-apps/logic-apps-diagnosing-failures.md)。

1. 確認邏輯應用程式已將電子郵件和附件儲存至正確的儲存體容器。

   1. 在儲存體總管中，依序展開 [Local & Attached] (本機與已連結的資源)  > [儲存體帳戶] > [attachmentstorageacct (金鑰)] > [Blob 容器] > [附件]。

   1. 檢查**附件**容器中是否有電子郵件和附件。

      ![檢查是否有已儲存的電子郵件和附件](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-attachments.png)

   1. 完成作業後，請刪除儲存體總管中的電子郵件和附件。

接下來，請新增讓邏輯應用程式傳送電子郵件以檢閱附件的動作。

## <a name="send-email-notifications"></a>傳送電子郵件通知

1. 在 [若為 true] 分支中的 [For each email attachment] 迴圈下，選取 [新增動作]。

   ![在 "for each" 迴圈下新增動作](./media/tutorial-process-email-attachments-workflow/add-action-send-email.png)

1. 在搜尋方塊中輸入 `send email` 做為篩選條件，然後為您的電子郵件提供者選取 [傳送電子郵件] 動作。

   若要篩選特定服務的動作清單，您可以先選取連接器。

   ![為您的電子郵件提供者選取「傳送電子郵件」動作](./media/tutorial-process-email-attachments-workflow/add-action-select-send-email.png)

   * 對於 Azure 工作或學校帳戶，選取 Office 365 Outlook。

   * 對於個人 Microsoft 帳戶，選取 Outlook.com。

1. 如果系統要求您提供認證，請登入您的電子郵件帳戶，讓 Logic Apps 能夠建立您電子郵件帳戶的連線。

1. 以下列說明為 [傳送電子郵件] 動作重新命名：`Send email for review`

1. 提供此動作的資訊，然後選取要包含在電子郵件中的欄位，說明如下。 若要在編輯方塊中新增空白的行，請按 Shift + Enter。

   ![傳送電子郵件通知](./media/tutorial-process-email-attachments-workflow/send-email-notification.png)

   如果您在動態內容清單中找不到預期的欄位，請選取 [新的電子郵件送達時] 旁的 [更多資訊]。

   | 設定 | 值 | 注意 |
   | ------- | ----- | ----- |
   | **若要** | <*recipient-email-address*> | 為了測試用途，您可以使用自己的電子郵件地址。 |
   | **主旨**  | ```ASAP - Review applicant for position:``` **主旨** | 您要包含的電子郵件主旨。 按一下此方塊內部，輸入範例文字，然後選取 [新的電子郵件送達時] 下方的 [主旨] 欄位。 |
   | **本文** | ```Please review new applicant:``` <p>```Applicant name:``` **寄件者** <p>```Application file location:``` **路徑** <p>```Application email content:``` **內文** | 電子郵件內文內容。 按一下此方塊內部，輸入範例文字，然後從動態內容清單中選取下列欄位： <p>- [新的電子郵件送達時] 下的 [寄件者] 欄位 </br>- [為電子郵件內文建立 Blob] 下的 [路徑] 欄位 </br>- [呼叫 RemoveHTMLFunction 以清除電子郵件內文] 下的 [內文] 欄位 |
   ||||

   > [!NOTE]
   > 如果您選取其中包含陣列的欄位 (例如 [內容] 欄位，這是包含附件的陣列)，設計工具會自動為參考該欄位的動作新增 "For each" 迴圈。
   > 如此一來，應用程式邏輯可以在每個陣列項目上執行該動作。
   > 若要移除迴圈，請移除陣列的欄位、將參考動作移至迴圈外、選取迴圈標題列上的省略符號 ( **...** )，然後選取 [刪除]。

1. 儲存您的邏輯應用程式。

現在，請測試您的邏輯應用程式，此時它看起來會如同下列範例：

![完成的邏輯應用程式](./media/tutorial-process-email-attachments-workflow/complete.png)

## <a name="run-your-logic-app"></a>執行邏輯應用程式

1. 將符合此準則的電子郵件傳送給自己：

   * 您的電子郵件主旨具有您在觸發程序的 [主旨篩選條件] 屬性中指定的文字：`Business Analyst 2 #423501`

   * 您的電子郵件有一或多個附件。 您可以重複使用先前測試使用的空文字檔。 為了讓案例更為真實，請附加履歷表檔案。

   * 電子郵件內文包含以下文字，您可以複製並貼上：

     ```text

     Name: Jamal Hartnett

     Street address: 12345 Anywhere Road

     City: Any Town

     State or Country: Any State

     Postal code: 00000

     Email address: jamhartnett@outlook.com

     Phone number: 000-000-0000

     Position: Business Analyst 2 #423501

     Technical skills: Dynamics CRM, MySQL, Microsoft SQL Server, JavaScript, Perl, Power BI, Tableau, Microsoft Office: Excel, Visio, Word, PowerPoint, SharePoint, and Outlook

     Professional skills: Data, process, workflow, statistics, risk analysis, modeling; technical writing, expert communicator and presenter, logical and analytical thinker, team builder, mediator, negotiator, self-starter, self-managing  

     Certifications: Six Sigma Green Belt, Lean Project Management

     Language skills: English, Mandarin, Spanish

     Education: Master of Business Administration
     ```

1. 執行邏輯應用程式。 如果成功，邏輯應用程式會傳送電子郵件給您，如下列範例所示：

   ![邏輯應用程式傳送的電子郵件通知](./media/tutorial-process-email-attachments-workflow/email-notification.png)

   若未收到任何電子郵件，請檢查電子郵件的垃圾郵件資料夾。 您的垃圾電子郵件篩選器可能會重新導向這類郵件。 或者，如果您不確定邏輯應用程式是否正確執行，請參閱[針對邏輯應用程式進行疑難排解](../logic-apps/logic-apps-diagnosing-failures.md)。

恭喜，您現在已建立並執行可在不同的 Azure 服務間自動執行工作並呼叫自訂程式碼的邏輯應用程式。

## <a name="clean-up-resources"></a>清除資源

如果不再需要此範例，請刪除包含邏輯應用程式的資源群組和相關資源。

1. 在頂層的 Azure 搜尋方塊中輸入 `resources groups`，然後選取 [資源群組]。

   ![尋找並選取「資源群組」](./media/tutorial-process-email-attachments-workflow/find-azure-resource-groups.png)

1. 從 [資源群組] 清單中，選取本教學課程的資源群組。 

   ![尋找教學課程的資源群組](./media/tutorial-process-email-attachments-workflow/find-select-tutorial-resource-group.png)

1. 在 [概觀] 窗格上，選取 [刪除資源群組]。

   ![刪除邏輯應用程式資源群組](./media/tutorial-process-email-attachments-workflow/delete-resource-group.png)

1. 當 [確認] 窗格出現時，輸入資源群組名稱，然後選取 [刪除]。

## <a name="next-steps"></a>後續步驟

在此教學課程中，您藉由 Azure 服務 (例如 Azure 儲存體和 Azure Functions) 的整合，建立了可處理及儲存電子郵件附件的邏輯應用程式。 現在，請深入了解可用來建置邏輯應用程式的其他連接器。

> [!div class="nextstepaction"]
> [深入了解 Logic Apps 的連接器](../connectors/apis-list.md)
