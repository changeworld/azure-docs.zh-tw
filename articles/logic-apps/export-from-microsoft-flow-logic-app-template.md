---
title: 從電源自動化到 Azure Logic Apps 的匯出流程
description: 藉由匯出為 Azure Resource Manager 範本，將流量從電源自動化遷移至 Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 616f10b32d0a9c1a05d759a0e27550cd2808808b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75428872"
---
# <a name="export-flows-from-power-automate-and-deploy-to-azure-logic-apps"></a>從 Power Automate 匯出流程，並部署至 Azure Logic Apps

若要擴充和擴充您的流程功能，您可以將該流程從[電源自動化](https://flow.microsoft.com)遷移到[Azure Logic Apps](../logic-apps/logic-apps-overview.md)。 您可以將流程匯出為邏輯應用程式的 Azure Resource Manager 範本、將該邏輯應用程式範本部署至 Azure 資源群組，然後在邏輯應用程式設計工具中開啟該邏輯應用程式。

> [!NOTE]
> 並非所有電源自動連接器都可在 Azure Logic Apps 中使用。 您可以在 Azure Logic Apps 中匯入具有對[等連接器](../connectors/apis-list.md)的流程。 例如，[按鈕觸發程式]、[核准連接器] 和 [通知連接器] 專用於電源自動化。
>
> 以 OpenAPI 為基礎的流程從電源自動化匯出目前不支援部署為邏輯應用程式範本。 

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 您想要從電源自動匯出的流程

## <a name="export-a-flow"></a>匯出流程

1. 登入[電源自動化](https://flow.microsoft.com)，然後選取 [**我的流程**]。 尋找並選取您的流程。 在工具列上，選取省略號（**...**）按鈕。 選取 [**匯出** > **Logic Apps 範本（. json）**]。

   ![匯出流程](./media/export-from-microsoft-flow-logic-app-template/export-flow.png)

1. 將您的範本儲存到您想要的位置。

如需詳細資訊，請參閱[成長到 Azure Logic Apps](https://flow.microsoft.com/blog/grow-up-to-logic-apps/)。

## <a name="deploy-template-by-using-the-azure-portal"></a>使用 Azure 入口網站部署範本

1. 使用您的 Azure 帳戶登入[Azure 入口網站](https://portal.azure.com)。

1. 在 Azure 主功能表上，選取 [建立資源]****。 在搜尋方塊中，輸入「範本部署」。 選取 **[範本部署（使用自訂範本部署）**]，然後選取 [**建立**]。

   ![選取 [範本部署]](./media/export-from-microsoft-flow-logic-app-template/select-template-deployment.png)

1. 在 [**自訂部署**] 底下，選取 **[在編輯器中建立您自己的範本**]。

   ![選取 [在編輯器中建立您自己的範本]](./media/export-from-microsoft-flow-logic-app-template/build-template-in-editor.png)

1. 從 [**編輯範本**] 工具列中，選取 [**載入**檔案]。 尋找並選取您從電源自動化匯出的 JSON 範本，然後選取 [**開啟**]。

   ![選取 [載入檔案]](./media/export-from-microsoft-flow-logic-app-template/load-file.png)

1. 在編輯器顯示您範本中的 JSON、參數和資源之後，請選取 [**儲存**]。
  
   ![儲存範本](./media/export-from-microsoft-flow-logic-app-template/save-template.png)

1. 現在，請為範本指定這些輸入參數：

   * 要用於計費的 Azure 訂用帳戶
   * Azure 資源群組
   * Azure 資源群組的位置
   * 邏輯應用程式資源的名稱
   * 邏輯應用程式資源的位置（如果與 Azure 資源群組不同）
   * 邏輯應用程式可以重複使用之任何先前建立的連線名稱

      如果您要建立第一個邏輯應用程式，所有連線都會建立為新的，因此您可以接受預設名稱。 否則，您可以為先前建立的連線指定名稱，以便在多個邏輯應用程式中使用。

   在您為範本提供此資訊之後，請檢查並同意建立必要 Azure 資源的 Azure Marketplace 條款及條件，並據以計費您的 Azure 訂用帳戶，然後選取 [**購買**]。
  
   ![指定範本的輸入參數](./media/export-from-microsoft-flow-logic-app-template/template-input-parameters.png)

   Azure 會將您的範本作為邏輯應用程式部署到您指定的資源群組。 您從電源自動化遷移的所有邏輯應用程式都會部署為停用狀態。

1. 在您啟動邏輯應用程式之前，請遵循下列步驟來授權任何新的連接：

   1. 開啟您所建立的邏輯應用程式。 在邏輯應用程式的功能表上，選取 [**邏輯應用程式設計**工具]。

      需要授權的每個連接都會顯示警告圖示：

      ![警告圖示](./media/export-from-microsoft-flow-logic-app-template/authorize-connections.png)

   1. 針對需要授權連接的每個步驟，展開該步驟，然後選取 [**新增**]。

      ![增加新的連線](./media/export-from-microsoft-flow-logic-app-template/add-new-connection.png)

   1. 登入每個服務，或提供必要的認證來授權連線。

1. 儲存您的邏輯應用程式。 當您準備好要啟動邏輯應用程式時，請在邏輯應用程式的功能表上，選取 **[總覽**]，然後選取 [**啟用**]。

   ![啟用邏輯應用程式](./media/export-from-microsoft-flow-logic-app-template/enable-logic-app.png)

1. 若要避免執行重複的工作流程，請確定您已停用或刪除原始流程。

## <a name="deploy-template-by-using-visual-studio"></a>使用 Visual Studio 部署範本

如果您已使用建立邏輯應用程式的[必要條件](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites)來設定 Visual Studio，您可以將匯出的範本從 Visual Studio 部署到 Azure Logic Apps。

1. 在 Visual Studio 中，開啟您從電源自動化匯出的範本檔案。

1. 在 Visual Studio 中，建立 Azure 資源群組專案，並遵循[快速入門：使用 Azure Logic Apps Visual Studio 建立自動化工作、進程和工作流程](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)中的步驟來選取**邏輯應用程式**範本，例如：

   ![建立 Azure 資源群組專案](./media/export-from-microsoft-flow-logic-app-template/create-azure-resource-group-project.png)

1. 從方案總管，如果檔案尚未開啟，請開啟**LogicApp**檔案。

1. 從匯出的範本複製內容，並覆寫**LogicApp**檔案中的內容。

1. 在您部署邏輯應用程式之前，請遵循下列步驟來授權任何新的連接：

   1. 開啟 [ **LogicApp** ] 快捷方式功能表，然後選取 [**使用邏輯應用程式設計工具開啟**]。

      ![使用邏輯應用程式設計工具開啟範本](./media/export-from-microsoft-flow-logic-app-template/open-logic-app-designer.png)

   1. 如果出現提示，請選取您想要用來部署邏輯應用程式的 Azure 訂用帳戶和資源群組。

      ![選取 Azure 訂用帳戶和資源群組](./media/export-from-microsoft-flow-logic-app-template/select-azure-subscription-resource-group-deployment.png)

      邏輯應用程式出現在設計工具中之後，任何需要授權的連接都會顯示警告圖示：

      ![具有警告圖示的連接](./media/export-from-microsoft-flow-logic-app-template/authorize-connections-vs.png)

   1. 針對需要授權連接的每個步驟，展開該步驟，然後選取 [**新增**]。

      ![增加新的連線](./media/export-from-microsoft-flow-logic-app-template/add-new-connection-vs.png)

   1. 登入每個服務，或提供必要的認證來授權連線。

   1. 部署邏輯應用程式之前，請先儲存您的解決方案。

1. 在方案總管中，開啟 [專案] 快捷方式功能表，然後選取 [**部署** > **新**的]。 如果出現提示，登入您的 Azure 帳戶。

1. 出現提示時，請確認 Azure 訂用帳戶、Azure 資源群組，以及您想要用於部署的任何其他設定，例如用來傳遞範本參數值的[參數](../azure-resource-manager/templates/parameter-files.md)檔案，然後選取 [**部署**]。

   ![確認部署設定](./media/export-from-microsoft-flow-logic-app-template/confirm-azure-subscription-resource-group-deployment.png)

1. 如果 [**編輯參數**] 方塊出現，請在 Azure 中提供邏輯應用程式資源的名稱，然後選取 [**儲存**]。  

   ![編輯部署參數](./media/export-from-microsoft-flow-logic-app-template/edit-parameters-deployment.png)

   開始部署時，您應用程式的部署狀態會顯示在 Visual Studio 的 [輸出]**** 視窗中。 如果狀態並未出現，請開啟 [顯示輸出來源]**** 清單，然後選取您的 Azure 資源群組。 例如：

   ![輸出視窗](./media/export-from-microsoft-flow-logic-app-template/output-window.png)

   如果邏輯應用程式中的任何連接需要您輸入，則會在背景中開啟 PowerShell 視窗，並提示您提供任何必要的密碼或秘密金鑰。 當您輸入這些資訊之後，部署將會繼續。

   ![驗證連接](./media/export-from-microsoft-flow-logic-app-template/logic-apps-powershell-window.png)

   部署完成之後，您的邏輯應用程式會發佈，但不會在 Azure 入口網站中啟用。

1. 當您準備好要在 Azure 入口網站中啟動邏輯應用程式時，請在邏輯應用程式設計工具中尋找並開啟邏輯應用程式。 在邏輯應用程式的功能表上，選取 **[總覽**]，然後選取 [**啟用**]。

1. 若要避免執行重複的工作流程，請確定您已停用或刪除原始流程。

如需這些部署步驟的詳細資訊，請參閱[快速入門：使用 Azure Logic Apps Visual Studio 建立自動化的工作、進程和工作流程](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-to-Azure)

## <a name="next-steps"></a>後續步驟

* 深入瞭解[Azure Logic Apps 的連接器](../connectors/apis-list.md)
* 深入瞭解[Azure Logic Apps](../logic-apps/logic-apps-overview.md)
