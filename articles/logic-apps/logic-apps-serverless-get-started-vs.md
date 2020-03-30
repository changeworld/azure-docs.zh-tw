---
title: 在視覺化工作室中構建第一個無伺服器應用程式
description: 在視覺化工作室中使用 Azure 邏輯應用和 Azure 功能構建、部署和管理無伺服器應用
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 06/20/2019
ms.openlocfilehash: 2e27958dd9379a26ca7e7f4d7e427e5afa216e29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981155"
---
# <a name="build-your-first-serverless-app-by-using-azure-logic-apps-and-azure-functions-in-visual-studio"></a>在視覺化工作室中使用 Azure 邏輯應用和 Azure 函數構建第一個無伺服器應用

通過使用 Azure 中的無伺服器工具和功能（如[Azure 邏輯應用](../logic-apps/logic-apps-overview.md)和[Azure 函數](../azure-functions/functions-overview.md)），可以快速開發和部署雲應用。 本文說明如何開始在 Visual Studio 中建置無伺服器應用程式，而該應用程式會使用可呼叫 Azure 函式的邏輯應用程式。 若要深入了解 Azure 中的無伺服器解決方案，請參閱[採用 Functions 和 Logic Apps 的 Azure 無伺服伺服器](../logic-apps/logic-apps-serverless-overview.md)。

## <a name="prerequisites"></a>Prerequisites

要在 Visual Studio 中構建無伺服器應用，您需要：

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 以下工具。 如果您還沒有它們，請下載並安裝它們。

  * [視覺工作室 2019， 2017， 或 2015 （社區或其他版本）.](https://aka.ms/download-visual-studio) 
  本快速入門使用免費的 Visual Studio Community 2017。

    > [!IMPORTANT]
    > 當您安裝 Visual Studio 2019 或 2017 時，請務必選取 **Azure 開發**工作負載。

  * [.NET 的 Microsoft Azure SDK（版本 2.9.1 或更高版本）。](https://azure.microsoft.com/downloads/) 
  深入了解 [Azure SDK for .NET](https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet)。

  * [Azure 電源外殼](https://github.com/Azure/azure-powershell#installation)。

  * 您想要的 Azure Logic Apps Tools for Visual Studio 版本：

    * [視覺工作室 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [視覺工作室 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    您可以直接從視覺化工作室應用商店下載和安裝 Azure 邏輯應用工具，也可以[瞭解如何從視覺化工作室內部安裝此擴展](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions)。 
    請務必在完成安裝之後重新啟動 Visual Studio。

  * [Azure 函數核心工具](https://www.npmjs.com/package/azure-functions-core-tools)，用於本地調試功能。

* 使用嵌入式邏輯應用設計器訪問 Web。

  設計工具需要網際網路連線才能在 Azure 中建立資源，以及從邏輯應用程式中的連接器讀取屬性和資料。 
  例如，如果您使用 Dynamics CRM Online 連接器，則設計工具會檢查您的 CRM 執行個體，以取得可用的預設和自訂屬性。

## <a name="create-a-resource-group-project"></a>創建資源組專案

若要開始進行，請為您的無伺服器應用程式建立 [Azure 資源群組專案](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)。 在 Azure 中，在*資源組*中創建資源 ，這是用於組織、管理和部署整個應用的資源作為單個資產的邏輯集合。 對於 Azure 中的無伺服器應用程式，您的資源群組包含 Azure Logic Apps 和 Azure Functions 資源。 深入了解 [Azure 資源群組和資源](../azure-resource-manager/management/overview.md)。

1. 使用 Azure 帳戶啟動視覺化工作室並登錄。

1. 在 [檔案]**** 功能表上，選取 [新增]**** > [專案]****。

   ![在 Visual Studio 中建立新專案](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. 在 [已安裝]**** 之下，選取 **Visual C#** 或 **Visual Basic**。 然後，選擇**雲** > **Azure 資源組**。

   > [!NOTE]
   > 如果**雲**類別或**Azure 資源組**專案不存在，請確保為視覺化工作室安裝了 Azure SDK。

   如果您使用 Visual Studio 2019，請遵循下列步驟：

   1. 在"**創建新專案**"框中，為 Visual C# 或 Visual Basic 選擇**Azure 資源組**專案範本，然後選擇 **"下一步**"。

   1. 提供要用於 Azure 資源組的名稱和其他專案資訊。 當您完成時，選取 [建立]****。

1. 為專案指定名稱和位置，然後選擇 **"確定**"。

   Visual Studio 會提示您從範本清單中選擇範本。 
   此示例使用 Azure 快速入門範本，以便可以生成包含邏輯應用和對 Azure 函數的調用的無伺服器應用。

   > [!TIP]
   > 在不希望將解決方案預部署到 Azure 資源組中的情況下，可以使用空白**邏輯應用**範本，該範本只需創建一個空邏輯應用。

1. 從 **"顯示此位置清單中的範本**"中，選擇**Azure 快速入門 （github.com/Azure/azure-quickstart-templates）。**

1. 在搜索框中，輸入"邏輯應用"作為篩選器。 從結果中選擇**101 邏輯應用和功能應用**範本。

   ![選擇 Azure 快速入門範本](./media/logic-apps-serverless-get-started-vs/select-template.png)

   Visual Studio 會為您的資源群組專案建立並開啟解決方案。 
   您選擇的 Azure 快速入門範本會在資源組專案中創建名為 azuredeploy.json 的部署範本。 此部署範本包括由 HTTP 要求觸發的簡單邏輯應用的定義，調用 Azure 函數，並將結果作為 HTTP 回應返回。

   ![新增無伺服器解決方案](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. 接下來，將解決方案部署到 Azure。 必須先執行此操作，然後才能打開部署範本並查看無伺服器應用的資源。

## <a name="deploy-your-solution"></a>部署您的解決方案

在視覺化工作室的邏輯應用設計器中打開邏輯應用之前，必須具有已在 Azure 中部署的 Azure 資源組。 設計工具可接著在您的邏輯應用程式中建立資源和服務的連線。 對於此任務，請按照以下步驟將解決方案從視覺化 Studio 部署到 Azure 門戶：

1. 在解決方案資源管理器中，從資源專案的快顯功能表中，選擇 **"部署** > **新建**"。

   ![建立資源群組的新部署](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. 如果尚未選擇它們，請選擇 Azure 訂閱和要部署到的資源組。 然後，選擇 **"部署**"。

   ![部署設定](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. 如果出現 **"編輯參數"** 框，請提供用於邏輯應用和 Azure 函數應用的資源名稱，然後保存設置。 確定您的函式應用程式使用全域唯一的名稱。

   ![提供邏輯應用程式和函式應用程式的名稱](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   當 Visual Studio 開始部署至您指定的資源群組時，您的解決方案部署狀態就會出現在 Visual Studio 的 [輸出]**** 視窗。 
   部署完成之後，您的邏輯應用程式會在 Azure 入口網站中上線。

## <a name="edit-your-logic-app-in-visual-studio"></a>在視覺化工作室中編輯邏輯應用

要在部署後編輯邏輯應用，請使用視覺化工作室中的邏輯應用設計器打開邏輯應用。

1. 在解決方案資源管理器中，從 azuredeploy.json 檔的快顯功能表中，選擇 **"使用邏輯應用設計器打開**"。

   ![在邏輯應用設計器中打開 azuredeploy.json](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

   > [!TIP]
   > 如果您在 Visual Studio 2019 中沒有此命令，請檢查您是否有最新的 Visual Studio 更新。

1. "**邏輯應用屬性**"框顯示後，請在 **"訂閱"** 下，選擇 Azure 訂閱（如果尚未選中）。 在 **"資源組**"下，選擇部署解決方案的資源組和位置，然後選擇 **"確定**"。

   ![邏輯應用程式屬性](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   在 Logic Apps 設計工具開啟之後，您就可以繼續新增步驟或變更工作流程，以及儲存您的更新。

   ![在 Logic Apps 設計工具中開啟的邏輯應用程式](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-your-azure-functions-project"></a>創建 Azure 函數專案

要使用 JavaScript、Python、F#、PowerShell、Batch 或 Bash 創建函數專案和函數，請按照使用[Azure 函數核心工具](../azure-functions/functions-run-local.md)中的步驟操作。 要使用解決方案中的 C# 開發 Azure 函數，請使用 C# 類庫，按照[將 .NET 類庫作為函數應用](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/)的步驟。

## <a name="deploy-functions-from-visual-studio"></a>從 Visual Studio 部署函式

部署範本從 Azuredeploy.json 檔中的變數指定的 Git 存儲庫中部署解決方案中的任何 Azure 函數。 如果在解決方案中創建和創作函數專案，則可以將該專案簽入 Git 原始程式碼管理（例如 GitHub 或 Azure DevOps），然後更新`repo`變數，以便範本部署 Azure 函數。

## <a name="manage-logic-apps-and-view-run-history"></a>管理邏輯應用程式及檢視執行歷程記錄

對於已在 Azure 中部署的邏輯應用，您仍然可以編輯、管理、查看 Visual Studio 中的執行歷程記錄和禁用這些應用。

1. 從 Visual Studio 中的 [檢視]**** 功能表，開啟 [Cloud Explorer]****。

1. 在 **"所有訂閱**"下，選擇與要管理的邏輯應用關聯的 Azure 訂閱，然後選擇 **"應用**"。

1. 在 [Logic Apps]**** 下，選取您的邏輯應用程式。 從該應用程式的捷徑功能表，選取 [使用邏輯應用程式編輯器開啟]****。

   > [!TIP]
   > 如果您在 Visual Studio 2019 中沒有此命令，請檢查您是否有最新的 Visual Studio 更新。

您現在可以將已經發佈的邏輯應用程式下載到您的資源群組專案中。 因此，儘管您可能在 Azure 門戶中啟動了邏輯應用，但仍可以在 Visual Studio 中導入和管理該應用。 如需詳細資訊，請參閱[使用 Visual Studio 管理邏輯應用程式](../logic-apps/manage-logic-apps-with-visual-studio.md)。

## <a name="next-steps"></a>後續步驟

* [使用 Visual Studio 管理邏輯應用程式](manage-logic-apps-with-visual-studio.md)
