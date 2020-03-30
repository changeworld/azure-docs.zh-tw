---
title: 使用視覺化工作室與雲資源管理器一起編輯和管理邏輯應用
description: 使用視覺化工作室與雲資源管理器一起編輯、更新、管理、添加到原始程式碼管理並部署邏輯應用
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 10/29/2019
ms.openlocfilehash: 6319d2d72df69cc9633bd2b2ff8e777c2a48966a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270247"
---
# <a name="manage-logic-apps-with-visual-studio"></a>使用 Visual Studio 管理邏輯應用程式

儘管可以在[Azure 門戶](https://portal.azure.com)中創建、編輯、管理和部署邏輯應用，但當您希望將邏輯應用添加到原始程式碼管理、發佈不同版本以及為各種部署環境創建[Azure 資源管理器](../azure-resource-manager/management/overview.md)範本時，也可以使用 Visual Studio。 使用 Visual Studio Cloud Explorer，您可以尋找並管理您的邏輯應用程式以及其他 Azure 資源。 例如，您可以開啟、下載、編輯、執行、檢視執行歷程記錄、停用和啟用已在 Azure 入口網站部署的邏輯應用程式。 如果您不曾在 Visual Studio 中使用 Azure Logic Apps，請了解[如何使用 Visual Studio 建立邏輯應用程式](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)。

> [!IMPORTANT]
> 從 Visual Studio 部署或發佈邏輯應用程式會覆寫該應用程式在 Azure 入口網站中的版本。 因此，如果您想要保留在 Azure 入口網站所做的變更，在下一次從 Visual Studio 部署或發佈之前，請確定您已從 Azure 入口網站[重新整理 Visual Studio 中的邏輯應用程式](#refresh)。

<a name="requirements"></a>

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 如果您還沒有以下這些工具，請加以下載並安裝：

  * [Visual Studio 2019、2017 或 2015 - Community 版本或更高版本](https://aka.ms/download-visual-studio)。 本快速入門使用免費的 Visual Studio Community 2017。

    > [!IMPORTANT]
    > 當您安裝 Visual Studio 2019 或 2017 時，請務必選取 **Azure 開發**工作負載。
    > 有關詳細資訊，請參閱[在視覺化工作室雲資源管理器中管理與 Azure 帳戶關聯的資源](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view)。

    要安裝雲資源管理器視覺工作室 2015，[從視覺化工作室市場下載雲資源管理器](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015). 有關詳細資訊，請參閱[在視覺化工作室雲資源管理器 （2015） 中管理與 Azure 帳戶關聯的資源](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view=vs-2015)。

  * [Azure SDK （2.9.1 或更高版本）](https://azure.microsoft.com/downloads/)

  * [Azure 電源外殼](https://github.com/Azure/azure-powershell#installation)

  * 適用於您所需版本之 Visual Studio 擴充功能的最新 Azure Logic Apps 工具：

    * [視覺工作室 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [視覺工作室 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)

    您可以直接從視覺化工作室應用商店下載和安裝 Azure 邏輯應用工具，也可以[瞭解如何從視覺化工作室內部安裝此擴展](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions)。 請務必在完成安裝之後重新啟動 Visual Studio。

* 使用內嵌的 Logic Apps 設計工具時能夠存取 Web

  設計工具需要網際網路連線才能在 Azure 中建立資源，以及從邏輯應用程式中的連接器讀取屬性和資料。 例如，如果您使用 Dynamics CRM Online 連接器，則設計工具會檢查您的 CRM 執行個體，以取得可用的預設和自訂屬性。

<a name="find-logic-apps-vs"></a>

## <a name="find-your-logic-apps"></a>尋找邏輯應用程式

您可以在 Visual Studio 中使用 Cloud Explorer，尋找與您的 Azure 訂用帳戶相關聯、且部署在 Azure 入口網站中的所有邏輯應用程式。

1. 開啟 Visual Studio。 在 [檢視]**** 功能表上選取 [Cloud Explorer]****。

1. 在雲資源管理器中，選擇**帳戶管理**。 選擇與邏輯應用關聯的 Azure 訂閱，然後選擇 **"應用**"。 例如：

   ![選擇"帳戶管理"](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

1. 根據您要依 [資源群組]**** 還是 [資源類型]**** 來搜尋，遵循下列相關步驟：

   * **資源群組**：在您的 Azure 訂用帳戶底下，Cloud Explorer 會顯示與該訂用帳戶相關聯的所有資源群組。 展開包含邏輯應用的資源組，然後選擇邏輯應用。

   * **資源類型**：在您的 Azure 訂用帳戶底下，展開 [邏輯應用程式]****。 在 Cloud Explorer 顯示與您訂用帳戶相關聯的所有已部署邏輯應用程式後，選取您的邏輯應用程式。

<a name="open-designer"></a>

## <a name="open-in-visual-studio"></a>在 Visual Studio 中開啟

在 Visual Studio 中，可以直接通過 Azure 門戶或作為 Visual Studio 的 Azure 資源組專案打開以前創建和部署的邏輯應用。

1. 開啟 Cloud Explorer，並尋找您的邏輯應用程式。

1. 在邏輯應用的快顯功能表上，選擇 **"使用邏輯應用編輯器打開**"。

   > [!TIP]
   > 如果您在 Visual Studio 2019 中沒有此命令，請檢查您是否有最新的 Visual Studio 更新。

   這個範例會顯示依資源類型所搜尋到的邏輯應用程式，因此您的邏輯應用程式會出現在 [邏輯應用程式]**** 區段下。

   ![從 Azure 入口網站開啟已部署的邏輯應用程式](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   邏輯應用在邏輯應用設計器中打開後，在設計器的底部，您可以選擇**代碼視圖**，以便可以查看基礎邏輯應用定義結構。 如果您想要為邏輯應用程式建立部署範本，請了解針對該邏輯應用程式要[如何下載 Azure Resource Manager 範本](#download-logic-app)。 深入了解 [Resource Manager 範本](../azure-resource-manager/templates/overview.md)。

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>從 Azure 下載

您可以從 [Azure 入口網站](https://portal.azure.com)下載邏輯應用程式，並將它們儲存為 [Azure Resource Manager](../azure-resource-manager/management/overview.md) 範本。 之後您可以在本機使用 Visual Studio 編輯範本，並針對不同部署環境自訂邏輯應用程式。  下載邏輯應用程式會自動將其在 [Resource Manager 範本](../azure-resource-manager/templates/overview.md)中的定義「參數化」**，這也會使用 JavaScript 物件標記法 (JSON)。

1. 在視覺化工作室中，打開雲資源管理器。 查找並選擇要從 Azure 下載的邏輯應用。

1. 在該應用程式的快顯功能表上，選擇 **"使用邏輯應用編輯器打開**"。

   > [!TIP]
   > 如果您在 Visual Studio 2019 中沒有此命令，請檢查您是否有最新的 Visual Studio 更新。

   邏輯應用程式設計工具隨即開啟，並顯示邏輯應用程式。 要查看邏輯應用的基礎定義和結構，請在設計器的底部選擇 **"代碼視圖**"。

1. 在設計器工具列上，選擇 **"下載**"。

   ![從 Azure 門戶下載邏輯應用](./media/manage-logic-apps-with-visual-studio/download-logic-app-from-portal.png)

1. 當系統提示您輸入位置時，瀏覽至該位置，並以 JSON (.json) 檔案格式儲存邏輯應用程式定義的 Resource Manager 範本。

   您的邏輯應用程式定義會出現在 Resource Manager 範本的 `resources` 子區段中。 您現在可以使用 Visual Studio 來編輯邏輯應用程式定義和 Resource Manager 範本。 還可以將範本作為[Azure 資源組專案](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)添加到視覺化工作室解決方案。 瞭解[視覺化工作室中邏輯應用的 Azure 資源組專案](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)。

<a name="link-integration-account"></a>

## <a name="link-to-integration-account"></a>連結到集成帳戶

要為企業對企業 （B2B） 企業集成方案構建邏輯應用，可以將邏輯應用連結到以前創建的與邏輯應用位於同一區域中的[集成帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。 集成帳戶包含 B2B 專案，如交易夥伴、協定、架構和映射，並允許邏輯應用使用 B2B 連接器進行 XML 驗證和一般檔案編碼或解碼。 儘管可以使用[Azure 門戶創建此連結](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)，但也可以在滿足[先決條件](#requirements)後使用 Visual Studio，並且邏輯應用作為 JSON （.json） 檔存在於 Azure[資源組專案中](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)。 瞭解[視覺化工作室中邏輯應用的 Azure 資源組專案](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#create-resource-group-project)。

1. 在視覺化工作室中，打開包含邏輯應用的 Azure 資源組專案。

1. 在解決方案資源管理器中，打開 **<邏輯應用名稱>.json**檔的快顯功能表，然後選擇 **"使用邏輯應用設計器打開**"。 （鍵盤： Ctrl = L）

   ![使用邏輯應用設計器打開邏輯應用的 .json 檔](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > 如果在 Visual Studio 2019 中沒有此命令，請檢查您是否具有對 Visual Studio 和 Azure 邏輯應用工具擴展的最新更新。

1. 通過選擇設計器的選項卡或曲面，確保邏輯應用設計器具有焦點，以便"屬性"視窗顯示邏輯應用的 **"集成帳戶"** 屬性。

   ![屬性視窗 - "集成帳戶"屬性](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-integration-account.png)

   > [!TIP]
   > 如果"屬性"視窗尚未打開，則從 **"視圖"** 功能表中選擇 **"屬性視窗**"。 （鍵盤：按 F4）

1. 打開**集成帳戶**屬性清單，然後選擇要連結到邏輯應用的集成帳戶，例如：

   ![打開"集成帳戶"屬性清單](./media/manage-logic-apps-with-visual-studio/select-integration-account.png)

1. 完成後，請記住保存您的 Visual Studio 解決方案。

在 Visual Studio 中設置**集成帳戶**屬性並將邏輯應用另存為 Azure 資源管理器範本時，該範本還包括所選集成帳戶的參數聲明。 有關範本參數和邏輯應用的詳細資訊，請參閱[概述：自動部署邏輯應用](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)。

<a name="change-location"></a>

## <a name="change-deployment-location"></a>更改部署位置

在 Visual Studio 中，如果邏輯應用作為 JSON （.json） 檔存在於用於自動部署的[Azure 資源組專案中](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)，則該邏輯應用將設置為位置類型和特定位置。 此位置是 Azure 區域或現有[整合服務環境 （ISE）。](connect-virtual-network-vnet-isolated-environment.md)

要更改邏輯應用的位置類型或位置，必須使用邏輯應用設計器從解決方案資源管理器打開邏輯應用的工作流定義 （.json） 檔。 無法使用雲資源管理器更改這些屬性。

> [!IMPORTANT]
> 將位置類型從**區域**更改為[**整合服務環境**](connect-virtual-network-vnet-isolated-environment-overview.md)會影響邏輯應用用於計費、[限制](logic-apps-limits-and-config.md#integration-account-limits)、[集成帳戶支援](connect-virtual-network-vnet-isolated-environment-overview.md#ise-skus)等的[定價模型](logic-apps-pricing.md#fixed-pricing)。 在選擇其他位置類型之前，請確保您瞭解對邏輯應用的影響。

1. 在視覺化工作室中，打開包含邏輯應用的 Azure 資源組專案。

1. 在解決方案資源管理器中`<logic-app-name>.json`，打開檔的快捷方式功能表，然後選擇 **"使用邏輯應用設計器打開**"。 （鍵盤： Ctrl = L）

   ![使用邏輯應用設計器打開邏輯應用的 .json 檔](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > 如果在 Visual Studio 2019 中沒有此命令，請檢查您是否具有對 Visual Studio 和 Azure 邏輯應用工具擴展的最新更新。

1. 通過選擇設計器的選項卡或曲面，確保邏輯應用設計器具有焦點，以便"屬性"視窗顯示邏輯應用的 **"選擇位置類型**和**位置**"屬性。 專案的位置類型設置為**區域**或**整合服務環境**。

   ![屬性視窗 - "選擇位置類型"&"位置"屬性](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-location.png)

   > [!TIP]
   > 如果"屬性"視窗尚未打開，則從 **"視圖"** 功能表中選擇 **"屬性視窗**"。 （鍵盤：按 F4）

1. 要更改位置類型，請打開 **"選擇位置類型"** 屬性清單，然後選擇所需的位置類型。

   例如，如果位置類型為**整合服務環境**，則可以選擇 **"區域**"。

   !["選擇位置類型"屬性 - 更改位置類型](./media/manage-logic-apps-with-visual-studio/change-location-type.png)

1. 要更改特定位置，打開 **"位置"** 屬性清單。 根據位置類型，選擇所需的位置，例如：

   * 選擇其他 Azure 區域：

     ![打開"位置"屬性清單，選擇其他 Azure 區域](./media/manage-logic-apps-with-visual-studio/change-azure-resource-group-region.png)

   * 選擇其他 ISE：

     ![打開"位置"屬性清單，選擇其他 ISE](./media/manage-logic-apps-with-visual-studio/change-integration-service-environment.png)

1. 完成後，請記住保存您的 Visual Studio 解決方案。

當您在 Visual Studio 中更改位置類型或位置並將邏輯應用另存為 Azure 資源管理器範本時，該範本還包括該位置類型和位置的參數聲明。 有關範本參數和邏輯應用的詳細資訊，請參閱[概述：自動部署邏輯應用](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)。

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>從 Azure 重新整理

如果您在 Azure 入口網站中編輯您的邏輯應用程式，並想要保留這些變更，請務必使用這些變更重新整理該應用程式在 Visual Studio 中的版本。

* 在視覺化工作室中，在邏輯應用設計器工具列上，選擇 **"刷新**"。

  -或-

* 在 Visual Studio Cloud Explorer 中，開啟邏輯應用程式的捷徑功能表，然後選取 [重新整理]****。

![使用更新內容重新整理邏輯應用程式](./media/manage-logic-apps-with-visual-studio/refresh-logic-app-with-updates-from-portal.png)

## <a name="publish-logic-app-updates"></a>發佈邏輯應用程式的更新

準備好將邏輯應用更新從 Visual Studio 部署到 Azure 時，請在邏輯應用設計器工具列上選擇 **"發佈**"。

![將更新的邏輯應用發佈到 Azure 門戶](./media/manage-logic-apps-with-visual-studio/publish-logic-app-to-azure-portal.png)

## <a name="manually-run-your-logic-app"></a>手動執行邏輯應用程式

您可以從 Visual Studio 手動觸發部署在 Azure 中的邏輯應用程式。 在邏輯應用設計器工具列上，選擇 **"運行觸發器**"。

![手動運行邏輯應用的觸發器](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>檢閱執行歷程記錄

若要檢查邏輯應用程式執行的狀態並診斷問題，您可以檢閱這些執行在 Visual Studio 中的詳細資料，例如輸入和輸出。

1. 在 Cloud Explorer 中，開啟邏輯應用程式的捷徑功能表，然後選取 [開啟執行歷程記錄]****。

   ![打開邏輯應用的執行歷程記錄](./media/manage-logic-apps-with-visual-studio/open-run-history-for-logic-app.png)

1. 若要檢閱特定執行的詳細資料，請對該執行按兩下。 例如：

   ![查看有關特定運行的資訊](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > 要按屬性對表進行排序，請選擇該屬性的列標題。

1. 展開要查看的輸入和輸出的步驟，例如：

   ![檢視每個步驟的輸入和輸出](./media/manage-logic-apps-with-visual-studio/view-run-history-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>停用或啟用邏輯應用程式

您可以在下一次觸發程序條件符合時停止引發觸發程序，而無需刪除您的邏輯應用程式。 停用邏輯應用程式可防止 Logic Apps 引擎建立和執行邏輯應用程式未來的工作流程執行個體。 在 Cloud Explorer 中，開啟邏輯應用程式的捷徑功能表，然後選取 [停用]****。

![在雲資源管理器中禁用邏輯應用](./media/manage-logic-apps-with-visual-studio/disable-logic-app-cloud-explorer.png)

> [!NOTE]
> 當您停用邏輯應用程式後，就不會具現化新的執行。 所有進行中和擱置的執行將會繼續直到完成，這可能需要一些時間。

要重新啟動邏輯應用，請在雲資源管理器中打開邏輯應用的快顯功能表，然後選擇"**啟用**"。

![在雲資源管理器中啟用邏輯應用](./media/manage-logic-apps-with-visual-studio/enable-logic-app-cloud-explorer.png)

## <a name="delete-your-logic-app"></a>刪除邏輯應用程式

若要從 Azure 入口網站刪除您的邏輯應用程式，請在 Cloud Explorer 中開啟邏輯應用程式的捷徑功能表，然後選取 [刪除]****。

![從 Azure 門戶中刪除邏輯應用](./media/manage-logic-apps-with-visual-studio/delete-logic-app-from-azure-portal.png)

> [!NOTE]
> 當您刪除邏輯應用程式後，就不會具現化新的執行。 所有進行中和擱置的執行都會取消。 如果您有數千個執行，加以取消可能需要很長的時間。 

## <a name="troubleshooting"></a>疑難排解

當您在 Logic Apps 設計工具中開啟您的邏輯應用程式專案時，您可能不會取得選取 Azure 訂用帳戶的選項。 相反地，您的邏輯應用程式以不是您想要使用的 Azure 訂用帳戶開啟。 此行為是因為當您開啟邏輯應用程式的 .json 檔案之後，Visual Studio 會快取第一個選取的訂用帳戶供未來使用。 若要解決此問題，請嘗試下列任一步驟：

* 重新命名邏輯應用程式的 .json 檔案。 訂用帳戶快取是根據檔案名稱。

* 要刪除解決方案中*所有*邏輯應用以前選定的訂閱，請刪除解決方案目錄中隱藏的 Visual Studio 設置資料夾 （.vs）。 此位置儲存您的訂用帳戶資訊。

## <a name="next-steps"></a>後續步驟

您已在本文中了解如何使用 Visual Studio 管理已部署的邏輯應用程式。 接下來，請了解如何針對部署自訂邏輯應用程式定義：

> [!div class="nextstepaction"]
> [以 JSON 撰寫邏輯應用程式定義](../logic-apps/logic-apps-author-definitions.md)
