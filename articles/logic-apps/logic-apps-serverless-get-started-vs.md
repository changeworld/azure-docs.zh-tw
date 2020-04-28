---
title: 在 Visual Studio 中建立第一個無伺服器應用程式
description: 使用中的 Azure Logic Apps 和 Azure Functions，來建立、部署和管理無伺服器應用程式 Visual Studio
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 06/20/2019
ms.openlocfilehash: 2e27958dd9379a26ca7e7f4d7e427e5afa216e29
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75981155"
---
# <a name="build-your-first-serverless-app-by-using-azure-logic-apps-and-azure-functions-in-visual-studio"></a>使用中的 Azure Logic Apps 和 Azure Functions，建立您的第一個無伺服器應用程式 Visual Studio

您可以使用 Azure 中的無伺服器工具和功能（例如[Azure Logic Apps](../logic-apps/logic-apps-overview.md)和[Azure Functions](../azure-functions/functions-overview.md)），快速開發及部署雲端應用程式。 本文說明如何開始在 Visual Studio 中建置無伺服器應用程式，而該應用程式會使用可呼叫 Azure 函式的邏輯應用程式。 若要深入了解 Azure 中的無伺服器解決方案，請參閱[採用 Functions 和 Logic Apps 的 Azure 無伺服伺服器](../logic-apps/logic-apps-serverless-overview.md)。

## <a name="prerequisites"></a>Prerequisites

若要在 Visual Studio 中建立無伺服器應用程式，您需要：

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 下列工具。 如果您還沒有這些專案，請下載並安裝它們。

  * [Visual Studio 2019、2017或2015（[社區] 或 [其他版本]）](https://aka.ms/download-visual-studio)。 
  本快速入門使用免費的 Visual Studio Community 2017。

    > [!IMPORTANT]
    > 當您安裝 Visual Studio 2019 或 2017 時，請務必選取 **Azure 開發**工作負載。

  * [Microsoft Azure SDK for .NET （2.9.1 或更新版本）](https://azure.microsoft.com/downloads/)。 
  深入了解 [Azure SDK for .NET](https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet)。

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)。

  * 您想要的 Azure Logic Apps Tools for Visual Studio 版本：

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    您可以直接從 Visual Studio Marketplace 下載並安裝 Azure Logic Apps 工具，或瞭解[如何從 Visual Studio 內部安裝此延伸](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions)模組。 
    請務必在完成安裝之後重新啟動 Visual Studio。

  * 本機偵錯工具的[Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools) 。

* 使用內嵌的邏輯應用程式設計工具時，存取 web。

  設計工具需要網際網路連線才能在 Azure 中建立資源，以及從邏輯應用程式中的連接器讀取屬性和資料。 
  例如，如果您使用 Dynamics CRM Online 連接器，則設計工具會檢查您的 CRM 執行個體，以取得可用的預設和自訂屬性。

## <a name="create-a-resource-group-project"></a>建立資源群組專案

若要開始進行，請為您的無伺服器應用程式建立 [Azure 資源群組專案](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)。 在 Azure 中，您會在*資源群組*內建立資源，這是一個邏輯集合，可用來將整個應用程式的資源組織、管理和部署為單一資產。 對於 Azure 中的無伺服器應用程式，您的資源群組包含 Azure Logic Apps 和 Azure Functions 資源。 深入了解 [Azure 資源群組和資源](../azure-resource-manager/management/overview.md)。

1. 啟動 Visual Studio，然後使用您的 Azure 帳戶登入。

1. 在 [檔案]**** 功能表上，選取 [新增]**** > [專案]****。

   ![在 Visual Studio 中建立新專案](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. 在 [已安裝]**** 之下，選取 **Visual C#** 或 **Visual Basic**。 然後，選取 [**雲端** > ] [**Azure 資源群組**]。

   > [!NOTE]
   > 如果**雲端**類別或**Azure 資源群組**專案不存在，請確定您已安裝 Azure SDK for Visual Studio。

   如果您使用 Visual Studio 2019，請遵循下列步驟：

   1. 在 [**建立新專案**] 方塊中，選取 [Visual c #] 或 [Visual Basic] 的 [ **Azure 資源群組**] 專案範本，然後選取 **[下一步]**。

   1. 提供您想要用於 Azure 資源群組的名稱和其他專案資訊。 當您完成時，選取 [建立]  。

1. 指定您的專案名稱和位置，然後選取 **[確定]**。

   Visual Studio 會提示您從 [範本] 清單中選取範本。 
   此範例使用 Azure 快速入門範本，讓您可以建立無伺服器應用程式，其中包含邏輯應用程式和 Azure 函數的呼叫。

   > [!TIP]
   > 在您不想將解決方案預先部署至 Azure 資源群組的案例中，您可以使用空白**邏輯應用程式**範本，這只會建立空的邏輯應用程式。

1. 從 [**顯示此位置的範本**] 清單中，選取 [ **Azure 快速入門（github.com/Azure/azure-quickstart-templates）**]。

1. 在搜尋方塊中，輸入「邏輯應用程式」作為篩選準則。 從結果中，選取 [ **101-邏輯應用**程式] 範本。

   ![選取 Azure 快速入門範本](./media/logic-apps-serverless-get-started-vs/select-template.png)

   Visual Studio 會為您的資源群組專案建立並開啟解決方案。 
   您選取的 Azure 快速入門範本會在您的資源群組專案中建立名為 azuredeploy.parameters.json」的部署範本。 此部署範本包含一個簡單邏輯應用程式的定義，由 HTTP 要求觸發、呼叫 Azure 函式，並以 HTTP 回應形式傳回結果。

   ![新增無伺服器解決方案](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. 接下來，將您的解決方案部署至 Azure。 您必須先執行此動作，才能開啟部署範本，並檢查無伺服器應用程式的資源。

## <a name="deploy-your-solution"></a>部署您的解決方案

在 Visual Studio 的邏輯應用程式設計工具中開啟邏輯應用程式之前，您必須擁有已在 Azure 中部署的 Azure 資源群組。 設計工具可接著在您的邏輯應用程式中建立資源和服務的連線。 針對這項工作，請遵循下列步驟，將您的方案從 Visual Studio 部署至 Azure 入口網站：

1. 在方案總管中，從資源專案的快捷方式功能表中，選取 [**部署** > **新**的]。

   ![建立資源群組的新部署](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. 如果尚未選取，請選取您的 Azure 訂用帳戶和您要部署的資源群組。 然後選取 [**部署**]。

   ![部署設定](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. 如果 [**編輯參數**] 方塊出現，請提供您的邏輯應用程式和您的 Azure 函式應用程式在部署時使用的資源名稱，然後儲存您的設定。 確定您的函式應用程式使用全域唯一的名稱。

   ![提供邏輯應用程式和函式應用程式的名稱](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   當 Visual Studio 開始部署至您指定的資源群組時，您的解決方案部署狀態就會出現在 Visual Studio 的 [輸出]**** 視窗。 
   部署完成之後，您的邏輯應用程式會在 Azure 入口網站中上線。

## <a name="edit-your-logic-app-in-visual-studio"></a>在 Visual Studio 中編輯邏輯應用程式

若要在部署之後編輯邏輯應用程式，請使用 Visual Studio 中的邏輯應用程式設計工具來開啟邏輯應用程式。

1. 在方案總管中，從 azuredeploy.parameters.json」的快捷方式功能表，選取 [**使用邏輯應用程式設計工具開啟**]。

   ![在邏輯應用程式設計工具中開啟 azuredeploy.parameters.json」](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

   > [!TIP]
   > 如果您在 Visual Studio 2019 中沒有此命令，請檢查您是否有最新的 Visual Studio 更新。

1. [**邏輯應用程式屬性**] 方塊出現之後，請在 [**訂**用帳戶] 底下，選取您的 Azure 訂用帳戶（如果尚未選取）。 在 [**資源群組**] 下，選取您部署解決方案的資源群組和位置，然後選取 **[確定]**。

   ![邏輯應用程式屬性](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   在 Logic Apps 設計工具開啟之後，您就可以繼續新增步驟或變更工作流程，以及儲存您的更新。

   ![在 Logic Apps 設計工具中開啟的邏輯應用程式](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-your-azure-functions-project"></a>建立您的 Azure Functions 專案

若要使用 JavaScript、Python、F #、PowerShell、Batch 或 Bash 來建立函式專案和函式，請遵循使用[Azure Functions Core Tools](../azure-functions/functions-run-local.md)中的步驟。 若要在您的方案中使用 c # 來開發 Azure 函式，請遵循將[.net 類別庫當做函數應用程式發行](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/)中的步驟來使用 c # 類別庫。

## <a name="deploy-functions-from-visual-studio"></a>從 Visual Studio 部署函式

您的部署範本會從 azuredeploy.parameters.json」中變數所指定的 Git 存放庫，部署您在解決方案中擁有的任何 Azure 函式。 如果您在方案中建立並撰寫函式專案，您可以將該專案簽入 Git 原始檔控制（例如 GitHub 或 Azure DevOps），然後更新`repo`變數，讓範本部署您的 Azure 函式。

## <a name="manage-logic-apps-and-view-run-history"></a>管理邏輯應用程式及檢視執行歷程記錄

針對已部署在 Azure 中的邏輯應用程式，您仍然可以編輯、管理、查看執行歷程記錄，並從 Visual Studio 停用這些應用程式。

1. 從 Visual Studio 中的 [檢視]**** 功能表，開啟 [Cloud Explorer]****。

1. 在 [**所有**訂用帳戶] 下 **，選取與**您想要管理之邏輯應用程式相關聯的 Azure 訂用帳戶，然後選取 [套用]。

1. 在 [Logic Apps]**** 下，選取您的邏輯應用程式。 從該應用程式的捷徑功能表，選取 [使用邏輯應用程式編輯器開啟]****。

   > [!TIP]
   > 如果您在 Visual Studio 2019 中沒有此命令，請檢查您是否有最新的 Visual Studio 更新。

您現在可以將已經發佈的邏輯應用程式下載到您的資源群組專案中。 因此，雖然您可能已在 Azure 入口網站中啟動邏輯應用程式，但仍可在 Visual Studio 中匯入和管理該應用程式。 如需詳細資訊，請參閱[使用 Visual Studio 管理邏輯應用程式](../logic-apps/manage-logic-apps-with-visual-studio.md)。

## <a name="next-steps"></a>後續步驟

* [使用 Visual Studio 管理邏輯應用程式](manage-logic-apps-with-visual-studio.md)
