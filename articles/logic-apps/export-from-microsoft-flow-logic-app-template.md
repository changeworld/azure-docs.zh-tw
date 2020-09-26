---
title: 將流程從 Power Automate 匯出至 Azure Logic Apps
description: 以 Azure Resource Manager 範本的形式匯出，將流程從 Power Automate 遷移至 Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 06/03/2020
ms.openlocfilehash: 1c2f0a2c54be7adbc7b8babd596f18e08c67a024
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91269551"
---
# <a name="export-flows-from-power-automate-and-deploy-to-azure-logic-apps"></a>從 Power Automate 匯出流程，並部署至 Azure Logic Apps

若要擴充和展開流程的功能，您可以將該流程從 [Power Automate](https://flow.microsoft.com) 遷移至 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)。 您可以將流程匯出為邏輯應用程式的 Azure Resource Manager 範本、將該邏輯應用程式範本部署至 Azure 資源群組，然後在邏輯應用程式設計工具中開啟該邏輯應用程式。

> [!NOTE]
> 並非所有 Power Automate 連接器都可在 Azure Logic Apps 中使用。 您只能遷移 Azure Logic Apps 中具有對等連接器的 Power Automate 流程。 例如，按鈕觸發程式、核准連接器和通知連接器都是 Power Automate 特有的。 目前，Power Automate 中以 OpenAPI 為基礎的流程不支援做為邏輯應用程式範本匯出和部署。
>
> * 若要找出哪些 Power Automate 連接器沒有 Logic Apps 對等專案，請參閱 [Power Automate 連接器](/connectors/connector-reference/connector-reference-powerautomate-connectors)。
>
> * 若要找出哪些 Logic Apps 連接器沒有 Power Automate 對等專案，請參閱 [Logic Apps 連接器](/connectors/connector-reference/connector-reference-powerautomate-connectors)。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 您要從 Power Automate 匯出的流程

## <a name="export-your-flow"></a>匯出您的流程

1. 登入 [Power Automate](https://flow.microsoft.com)，然後選取 [ **我的流程**]。 尋找並選取您的流程。 在工具列上，選取省略號 (**...**) 按鈕 >**匯出**  >  **Logic Apps 範本 (. json) **。

   ![從 Power Automate 匯出流程](./media/export-from-microsoft-flow-logic-app-template/export-flow.png)

1. 將範本的 json 檔案儲存至您想要的位置。

如需詳細資訊，請參閱 [擴大至 Azure Logic Apps](https://flow.microsoft.com/blog/grow-up-to-logic-apps/)。

## <a name="deploy-template-by-using-the-azure-portal"></a>使用 Azure 入口網站部署範本

1. 使用您的 Azure 帳戶登入 [Azure 入口網站](https://portal.azure.com) 。

1. 在 Azure 首頁的 [搜尋] 方塊中，輸入 `custom template` 。 從結果中，選取 [**部署自訂範本**  >  **建立**]。

   ![尋找並選取 [範本部署]](./media/export-from-microsoft-flow-logic-app-template/select-template-deployment.png)

1. 在 [ **自訂部署**] 底下，選取 **[在編輯器中建立您自己的範本**]。

   ![選取 [在編輯器中建立您自己的範本]](./media/export-from-microsoft-flow-logic-app-template/build-template-in-editor.png)

1. 在 [ **編輯範本** ] 工具列上，選取 [ **載入**檔案]。

   ![選取 [載入檔案]](./media/export-from-microsoft-flow-logic-app-template/load-file.png)

1. 流覽至您儲存從 Power Automate 匯出的 JSON 範本檔案的位置。 選取 > **開啟**的範本檔案。

1. 在編輯器顯示範本中的 JSON、參數和資源之後，請選取 [ **儲存**]。

   ![儲存範本](./media/export-from-microsoft-flow-logic-app-template/save-template.png)

1. 現在提供邏輯應用程式的詳細資訊。

   1. 選取或指定範本的輸入參數值。

      | 屬性 | 說明 |
      |----------|-------------|
      | **訂用帳戶** | 要用於計費的 Azure 訂用帳戶 |
      | **資源群組** | 要用於邏輯應用程式的 Azure 資源群組。 您可以使用現有的群組或建立新的群組。 |
      | **位置** | 當您建立新的資源群組時，所要使用的 Azure 區域 |
      | **邏輯應用程式名稱** | 要用於邏輯應用程式資源的名稱 |
      | **邏輯應用程式位置** | 您要在其中建立邏輯應用程式資源的 Azure 區域（如果與 Azure 資源群組不同） |
      | <*連接-名稱*> | 邏輯應用程式可重複使用的任何先前建立連線的一或多個名稱 <p><p>**注意**：如果此邏輯應用程式是您的第一個，則所有連接都會建立為新的，因此您可以接受預設名稱。 否則，您可以為先前建立的連線指定名稱，您可以在多個邏輯應用程式中使用這些名稱。 |
      |||

      例如：

      ![指定範本的輸入參數](./media/export-from-microsoft-flow-logic-app-template/template-input-parameters.png)

   1. 當您完成時，請參閱建立必要 Azure 資源的 **條款及條件** ，並據此向您的 azure 訂用帳戶計費。

   1. 當您準備好時，請選取 **[我同意上方所述的條款及條件**]  >  ** **。

      Azure 會將您的範本作為邏輯應用程式部署到指定的資源群組。

1. 您從 Power Automate 遷移的所有邏輯應用程式都會部署為停用狀態。 啟用邏輯應用程式之前，請遵循下列步驟來授權任何新的連接：

   1. 在 Azure 入口網站中，開啟您所建立的邏輯應用程式。 在邏輯應用程式的功能表上，選取 [ **邏輯應用程式設計**工具]。

      每個需要授權的連接都會顯示警告圖示：

      ![警告圖示](./media/export-from-microsoft-flow-logic-app-template/authorize-connections.png)

   1. 針對需要授權連接的每個步驟，展開該步驟，然後選取 [ **加入新**的]。

      ![顯示在 Outlook [連接] 視窗上選取 [新增] 按鈕的螢幕擷取畫面。](./media/export-from-microsoft-flow-logic-app-template/add-new-connection.png)

   1. 登入每個服務，或提供必要的認證以授權連接。

   1. 更新連接之後，請在設計工具的工具列上選取 [ **儲存**]。

1. 當您準備好啟用邏輯應用程式時，請在邏輯應用程式的功能表上，選取 **[總覽**]，然後選取 [ **啟用**]。

   ![啟用邏輯應用程式](./media/export-from-microsoft-flow-logic-app-template/enable-logic-app.png)

1. 若要避免執行重複的工作流程，請確定您已停用或刪除原始流程。

## <a name="deploy-template-by-using-visual-studio"></a>使用 Visual Studio 部署範本

如果您已使用建立邏輯應用程式的 [必要條件](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites) 設定 Visual Studio，您可以使用 Visual Studio，將匯出的範本部署到 Azure Logic Apps。

1. 在 Visual Studio 中，流覽至您從 Power Automate 匯出的邏輯應用程式範本的 json 檔案並加以開啟。

1. 在 Visual Studio 中，[依照快速入門：使用 Azure Logic Apps Visual Studio 建立自動化工作、進程和工作流程](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)中的步驟，建立使用**邏輯應用程式**範本的**Azure 資源群組**專案。

   這個範例會建立名為 "ImportedLogicApp" 的 Visual Studio 方案。

   ![建立 Azure 資源群組專案](./media/export-from-microsoft-flow-logic-app-template/create-azure-resource-group-project.png)

1. 建立方案之後，在方案總管中，開啟檔案 ** 上的LogicApp.js** （如果檔案尚未開啟）。

1. 從匯出的範本複製內容，並覆寫檔案 **LogicApp.js** 中的內容。

1. 部署邏輯應用程式之前，請遵循下列步驟來授權任何新的連接：

   1. 開啟快捷方式功能表上的 [ **LogicApp.js** ]，然後選取 [ **使用邏輯應用程式設計工具開啟**]。

      ![使用邏輯應用程式設計工具開啟範本](./media/export-from-microsoft-flow-logic-app-template/open-logic-app-designer.png)

   1. 如果出現提示，請選取您要用來部署邏輯應用程式的 Azure 訂用帳戶和資源群組。

      ![選取 Azure 訂用帳戶和資源群組](./media/export-from-microsoft-flow-logic-app-template/select-azure-subscription-resource-group-deployment.png)

      在設計工具中出現邏輯應用程式之後，任何需要授權的連接都會顯示警告圖示：

      ![具有警告圖示的連接](./media/export-from-microsoft-flow-logic-app-template/authorize-connections-vs.png)

   1. 針對需要授權連接的每個步驟，展開該步驟，然後選取 [ **加入新**的]。

      ![增加新的連線](./media/export-from-microsoft-flow-logic-app-template/add-new-connection-vs.png)

   1. 登入每個服務，或提供必要的認證以授權連接。

   1. 部署邏輯應用程式之前，請先儲存您的解決方案。

1. 在方案總管中，開啟專案快捷方式功能表，然後選取 [**部署**  >  **新**專案]。 如果出現提示，登入您的 Azure 帳戶。

1. 出現提示時，請確認 Azure 訂用帳戶、Azure 資源群組，以及您想要用於部署的任何其他設定，例如用於傳遞範本參數值的 [參數](../azure-resource-manager/templates/parameter-files.md) 檔案，然後選取 [ **部署**]。

   ![確認部署設定](./media/export-from-microsoft-flow-logic-app-template/confirm-azure-subscription-resource-group-deployment.png)

1. 如果出現 [ **編輯參數** ] 方塊，請在 Azure 中提供邏輯應用程式資源的名稱，然後選取 [ **儲存**]。  

   ![編輯部署參數](./media/export-from-microsoft-flow-logic-app-template/edit-parameters-deployment.png)

   開始部署時，您應用程式的部署狀態會顯示在 Visual Studio 的 [輸出] 視窗中。 如果狀態並未出現，請開啟 [顯示輸出來源] 清單，然後選取您的 Azure 資源群組。 例如：

   ![輸出視窗](./media/export-from-microsoft-flow-logic-app-template/output-window.png)

   如果邏輯應用程式中的任何連線需要您輸入，則會在背景中開啟 PowerShell 視窗，並提示您輸入任何必要的密碼或秘密金鑰。 當您輸入這些資訊之後，部署將會繼續。

   ![驗證連接](./media/export-from-microsoft-flow-logic-app-template/logic-apps-powershell-window.png)

   部署完成之後，您的邏輯應用程式會發佈，但不會在 Azure 入口網站中啟用。

1. 當您準備好要在 Azure 入口網站中啟用邏輯應用程式時，請在邏輯應用程式設計工具中尋找並開啟您的邏輯應用程式。 在邏輯應用程式的功能表上，選取 **[總覽**]，然後選取 [ **啟用**]。

1. 若要避免執行重複的工作流程，請確定您已停用或刪除原始流程。

如需這些部署步驟的詳細資訊，請參閱 [快速入門：使用 Azure Logic Apps Visual Studio 建立自動化工作、進程和工作流程](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-to-Azure)

## <a name="next-steps"></a>後續步驟

* 深入瞭解 [Azure Logic Apps 的連接器](../connectors/apis-list.md)
* 深入瞭解 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)
