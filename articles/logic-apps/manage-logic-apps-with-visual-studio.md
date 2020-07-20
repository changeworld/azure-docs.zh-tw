---
title: 使用 Visual Studio 搭配 Cloud Explorer 來編輯和管理邏輯應用程式
description: 編輯、更新、管理、新增至原始檔控制，以及使用 Visual Studio 搭配 Cloud Explorer 部署邏輯應用程式
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 04/29/2020
ms.openlocfilehash: 62807ef25fe6ee70844e72c90751eb383d0ece7c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82598431"
---
# <a name="manage-logic-apps-with-visual-studio"></a>使用 Visual Studio 管理邏輯應用程式

雖然您可以在[Azure 入口網站](https://portal.azure.com)中建立、編輯、管理和部署邏輯應用程式，但當您想要將邏輯應用程式新增至原始檔控制、發佈不同的版本，以及為各種部署環境建立[Azure Resource Manager](../azure-resource-manager/management/overview.md)範本時，也可以使用 Visual Studio。 使用 Visual Studio Cloud Explorer，您可以尋找並管理您的邏輯應用程式以及其他 Azure 資源。 例如，您可以開啟、下載、編輯、執行、檢視執行歷程記錄、停用和啟用已在 Azure 入口網站部署的邏輯應用程式。 如果您不曾在 Visual Studio 中使用 Azure Logic Apps，請了解[如何使用 Visual Studio 建立邏輯應用程式](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)。

您也可以[在 Azure 入口網站中管理邏輯應用程式](manage-logic-apps-with-azure-portal.md)。

> [!IMPORTANT]
> 從 Visual Studio 部署或發佈邏輯應用程式會覆寫該應用程式在 Azure 入口網站中的版本。 因此，如果您想要保留在 Azure 入口網站所做的變更，在下一次從 Visual Studio 部署或發佈之前，請確定您已從 Azure 入口網站[重新整理 Visual Studio 中的邏輯應用程式](#refresh)。

<a name="requirements"></a>

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 如果您還沒有以下這些工具，請加以下載並安裝：

  * [Visual Studio 2019、2017 或 2015 - Community 版本或更高版本](https://aka.ms/download-visual-studio)。 本快速入門使用免費的 Visual Studio Community 2017。

    > [!IMPORTANT]
    > 當您安裝 Visual Studio 2019 或 2017 時，請務必選取 **Azure 開發**工作負載。
    > 如需詳細資訊，請參閱[Visual Studio Cloud Explorer 中的管理與您的 Azure 帳戶相關聯的資源](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view)。

    若要安裝 Visual Studio 2015 的 Cloud Explorer，請[從 Visual Studio Marketplace 下載 Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015)。 如需詳細資訊，請參閱[在 Visual Studio Cloud Explorer 中管理與您的 Azure 帳戶相關聯的資源（2015）](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view=vs-2015)。

  * [Azure SDK （2.9.1 或更新版本）](https://azure.microsoft.com/downloads/)

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * 適用於您所需版本之 Visual Studio 擴充功能的最新 Azure Logic Apps 工具：

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)

    您可以直接從 Visual Studio Marketplace 下載並安裝 Azure Logic Apps 工具，或瞭解[如何從 Visual Studio 內部安裝此延伸](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions)模組。 請務必在完成安裝之後重新啟動 Visual Studio。

* 使用內嵌的 Logic Apps 設計工具時能夠存取 Web

  設計工具需要網際網路連線才能在 Azure 中建立資源，以及從邏輯應用程式中的連接器讀取屬性和資料。 例如，如果您使用 Dynamics CRM Online 連接器，則設計工具會檢查您的 CRM 執行個體，以取得可用的預設和自訂屬性。

<a name="find-logic-apps-vs"></a>

## <a name="find-your-logic-apps"></a>尋找邏輯應用程式

您可以在 Visual Studio 中使用 Cloud Explorer，尋找與您的 Azure 訂用帳戶相關聯、且部署在 Azure 入口網站中的所有邏輯應用程式。

1. 開啟 Visual Studio。 在 [檢視]**** 功能表上選取 [Cloud Explorer]****。

1. 在 Cloud Explorer 中，選取 [**帳戶管理**]。 選取與您的邏輯應用程式相關聯的 Azure 訂用帳戶，然後選取 [套用 **]。** 例如：

   ![選取 [帳戶管理]](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

1. 根據您要依 [資源群組]**** 還是 [資源類型]**** 來搜尋，遵循下列相關步驟：

   * **資源群組**：在您的 Azure 訂用帳戶底下，Cloud Explorer 會顯示與該訂用帳戶相關聯的所有資源群組。 展開包含邏輯應用程式的資源群組，然後選取您的邏輯應用程式。

   * **資源類型**：在您的 Azure 訂用帳戶底下，展開 [邏輯應用程式]****。 在 Cloud Explorer 顯示與您訂用帳戶相關聯的所有已部署邏輯應用程式後，選取您的邏輯應用程式。

<a name="open-designer"></a>

## <a name="open-in-visual-studio"></a>在 Visual Studio 中開啟

在 Visual Studio 中，您可以開啟先前建立的邏輯應用程式，並直接透過 Azure 入口網站或 Azure 資源群組專案與 Visual Studio 來加以部署。

1. 開啟 Cloud Explorer，並尋找您的邏輯應用程式。

1. 在邏輯應用程式的快捷方式功能表上，選取 [**使用邏輯應用程式編輯器開啟**]。

   > [!TIP]
   > 如果您在 Visual Studio 2019 中沒有此命令，請檢查您是否有最新的 Visual Studio 更新。

   這個範例會顯示依資源類型所搜尋到的邏輯應用程式，因此您的邏輯應用程式會出現在 [邏輯應用程式]**** 區段下。

   ![從 Azure 入口網站開啟已部署的邏輯應用程式](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   在 Logic Apps 設計工具中開啟邏輯應用程式之後，您可以在設計工具的底部選取 [程式**代碼視圖**]，讓您可以查看基礎邏輯應用程式定義結構。 如果您想要為邏輯應用程式建立部署範本，請了解針對該邏輯應用程式要[如何下載 Azure Resource Manager 範本](#download-logic-app)。 深入瞭解[Resource Manager 範本](../azure-resource-manager/templates/overview.md)。

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>從 Azure 下載

您可以從 [Azure 入口網站](https://portal.azure.com)下載邏輯應用程式，並將它們儲存為 [Azure Resource Manager](../azure-resource-manager/management/overview.md) 範本。 之後您可以在本機使用 Visual Studio 編輯範本，並針對不同部署環境自訂邏輯應用程式。  下載邏輯應用程式會自動將其在 [Resource Manager 範本](../azure-resource-manager/templates/overview.md)中的定義「參數化」**，這也會使用 JavaScript 物件標記法 (JSON)。

1. 在 Visual Studio 中，開啟 Cloud Explorer。 尋找並選取您想要從 Azure 下載的邏輯應用程式。

1. 在該應用程式的快捷方式功能表上，選取 [**使用邏輯應用程式編輯器開啟**]。

   > [!TIP]
   > 如果您在 Visual Studio 2019 中沒有此命令，請檢查您是否有最新的 Visual Studio 更新。

   邏輯應用程式設計工具隨即開啟，並顯示邏輯應用程式。 若要檢查邏輯應用程式的基礎定義和結構，請在設計工具的底部選取 [程式**代碼視圖**]。

1. 在設計工具工具列上，選取 [**下載**]。

   ![從 Azure 入口網站下載邏輯應用程式](./media/manage-logic-apps-with-visual-studio/download-logic-app-from-portal.png)

1. 當系統提示您輸入位置時，瀏覽至該位置，並以 JSON (.json) 檔案格式儲存邏輯應用程式定義的 Resource Manager 範本。

   您的邏輯應用程式定義會出現在 Resource Manager 範本的 `resources` 子區段中。 您現在可以使用 Visual Studio 來編輯邏輯應用程式定義和 Resource Manager 範本。 您也可以將範本做為[Azure 資源群組專案](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)新增至 Visual Studio 解決方案。 [在 Visual Studio 中瞭解邏輯應用程式的 Azure 資源群組專案](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)。

<a name="link-integration-account"></a>

## <a name="link-to-integration-account"></a>連結至整合帳戶

若要建立企業對企業（B2B）企業整合案例的邏輯應用程式，您可以將邏輯應用程式連結至與邏輯應用程式位於相同區域的先前建立[整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。 整合帳戶包含 B2B 成品（例如交易夥伴、合約、架構及對應），可讓您的邏輯應用程式使用 B2B 連接器進行 XML 驗證和一般檔案編碼或解碼。 雖然您可以[使用 Azure 入口網站建立此連結](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)，您也可以在符合[必要條件](#requirements)之後使用 Visual Studio，而邏輯應用程式會以 json （json）檔案的形式存在於[Azure 資源群組專案](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)內。 [在 Visual Studio 中瞭解邏輯應用程式的 Azure 資源群組專案](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#create-resource-group-project)。

1. 在 Visual Studio 中，開啟包含邏輯應用程式的 Azure 資源群組專案。

1. 在方案總管中，開啟檔案快捷方式功能表**上的<邏輯-應用程式名稱 # C1.js** ，然後選取 [**使用邏輯應用程式設計工具開啟**]。 （鍵盤： Ctrl + L）

   ![使用邏輯應用程式設計工具開啟邏輯應用程式的 json 檔案](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > 如果您在 Visual Studio 2019 中沒有此命令，請檢查您是否有 Visual Studio 的最新更新和 Azure Logic Apps 工具延伸模組。

1. 請確定邏輯應用程式設計工具具有焦點，方法是選取設計工具的索引標籤或介面，讓屬性視窗顯示邏輯應用程式的**整合帳戶**屬性。

   ![屬性視窗-「整合帳戶」屬性](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-integration-account.png)

   > [!TIP]
   > 如果屬性視窗尚未開啟，請從 [ **View** ] 功能表中選取 [**屬性視窗]**。 （鍵盤：按 F4）

1. 開啟**整合帳戶**屬性清單，然後選取您想要連結到邏輯應用程式的整合帳戶，例如：

   ![開啟 [整合帳戶] 屬性清單](./media/manage-logic-apps-with-visual-studio/select-integration-account.png)

1. 當您完成時，請記得儲存您的 Visual Studio 解決方案。

當您在 Visual Studio 中設定**整合帳戶**屬性，並將邏輯應用程式儲存為 Azure Resource Manager 範本時，該範本也會包含所選整合帳戶的參數宣告。 如需範本參數和邏輯應用程式的詳細資訊，請參閱[總覽：自動化邏輯應用程式部署](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)。

<a name="change-location"></a>

## <a name="change-deployment-location"></a>變更部署位置

在 Visual Studio 中，如果您的邏輯應用程式是以 JSON （. json）檔案的形式存在於您用來自動化部署的[Azure 資源群組專案](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)中，則該邏輯應用程式會設定為位置類型和特定位置。 此位置可以是 Azure 區域或現有的[整合服務環境（ISE）](connect-virtual-network-vnet-isolated-environment.md)。

若要變更邏輯應用程式的位置類型或位置，您必須使用邏輯應用程式設計工具，從方案總管開啟邏輯應用程式的工作流程定義（json）檔案。 您無法使用 Cloud Explorer 來變更這些屬性。

> [!IMPORTANT]
> 將位置類型從**區域**變更為[**整合服務環境**](connect-virtual-network-vnet-isolated-environment-overview.md)，會影響邏輯應用程式的[定價模型](logic-apps-pricing.md#fixed-pricing)，用於計費、[限制](logic-apps-limits-and-config.md#integration-account-limits)、[整合帳戶支援](connect-virtual-network-vnet-isolated-environment-overview.md#ise-skus)等等。 在您選取不同的位置類型之前，請確定您瞭解對邏輯應用程式所產生的影響。

1. 在 Visual Studio 中，開啟包含邏輯應用程式的 Azure 資源群組專案。

1. 在方案總管中，開啟檔案 `<logic-app-name>.json` 的快捷方式功能表，然後選取 [**使用邏輯應用程式設計工具開啟**]。 （鍵盤： Ctrl + L）

   ![使用邏輯應用程式設計工具開啟邏輯應用程式的 json 檔案](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > 如果您在 Visual Studio 2019 中沒有此命令，請檢查您是否有 Visual Studio 的最新更新和 Azure Logic Apps 工具延伸模組。

1. 請確定邏輯應用程式設計工具具有焦點，方法是選取設計工具的索引標籤或介面，讓屬性視窗顯示邏輯應用程式的 **[選擇位置類型**] 和 [**位置**] 屬性。 專案的 [位置類型] 設定為 [**區域**] 或 [**整合服務環境**]。

   ![屬性視窗-「選擇位置類型」 & 「位置」屬性](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-location.png)

   > [!TIP]
   > 如果屬性視窗尚未開啟，請從 [ **View** ] 功能表中選取 [**屬性視窗]**。 （鍵盤：按 F4）

1. 若要變更位置類型，請開啟 [**選擇位置類型**] 屬性清單，然後選取您想要的位置類型。

   例如，如果 [位置類型] 是 [**整合服務環境**]，您可以選取 [**區域**]。

   ![[選擇位置類型] 屬性-變更位置類型](./media/manage-logic-apps-with-visual-studio/change-location-type.png)

1. 若要變更特定位置，請開啟 [**位置**] 屬性清單。 根據 [位置類型]，選取您想要的位置，例如：

   * 選取不同的 Azure 區域：

     ![開啟 [位置] 屬性清單，選取另一個 Azure 區域](./media/manage-logic-apps-with-visual-studio/change-azure-resource-group-region.png)

   * 選取不同的 ISE：

     ![開啟 [位置] 屬性清單，選取另一個 ISE](./media/manage-logic-apps-with-visual-studio/change-integration-service-environment.png)

1. 當您完成時，請記得儲存您的 Visual Studio 解決方案。

當您在 Visual Studio 中變更位置類型或位置，並將邏輯應用程式儲存為 Azure Resource Manager 範本時，該範本也會包含該位置類型和位置的參數宣告。 如需範本參數和邏輯應用程式的詳細資訊，請參閱[總覽：自動化邏輯應用程式部署](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)。

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>從 Azure 重新整理

如果您在 Azure 入口網站中編輯您的邏輯應用程式，並想要保留這些變更，請務必使用這些變更重新整理該應用程式在 Visual Studio 中的版本。

* 在 Visual Studio 的邏輯應用程式設計工具工具列上，**選取 [** 重新整理]。

  -或-

* 在 Visual Studio Cloud Explorer 中，開啟邏輯應用程式的捷徑功能表，然後選取 [重新整理]****。

![使用更新內容重新整理邏輯應用程式](./media/manage-logic-apps-with-visual-studio/refresh-logic-app-with-updates-from-portal.png)

## <a name="publish-logic-app-updates"></a>發佈邏輯應用程式的更新

當您準備好要將邏輯應用程式更新從 Visual Studio 部署到 Azure 時，請在邏輯應用程式設計工具工具列上，選取 [**發佈**]。

![將已更新的邏輯應用程式發佈至 Azure 入口網站](./media/manage-logic-apps-with-visual-studio/publish-logic-app-to-azure-portal.png)

## <a name="manually-run-your-logic-app"></a>手動執行邏輯應用程式

您可以從 Visual Studio 手動觸發部署在 Azure 中的邏輯應用程式。 在邏輯應用程式設計工具工具列上，選取 [**執行觸發**程式]。

![手動執行邏輯應用程式的觸發程式](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>檢閱執行歷程記錄

若要檢查邏輯應用程式執行的狀態並診斷問題，您可以檢閱這些執行在 Visual Studio 中的詳細資料，例如輸入和輸出。

1. 在 Cloud Explorer 中，開啟邏輯應用程式的捷徑功能表，然後選取 [開啟執行歷程記錄]****。

   ![開啟邏輯應用程式的執行歷程記錄](./media/manage-logic-apps-with-visual-studio/open-run-history-for-logic-app.png)

1. 若要檢閱特定執行的詳細資料，請對該執行按兩下。 例如：

   ![查看特定執行的相關資訊](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > 若要依屬性排序資料表，請選取該屬性的欄標題。

1. 展開您要檢查其輸入和輸出的步驟，例如：

   ![檢視每個步驟的輸入和輸出](./media/manage-logic-apps-with-visual-studio/view-run-history-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>停用或啟用邏輯應用程式

您可以在下一次觸發程序條件符合時停止引發觸發程序，而無需刪除您的邏輯應用程式。 停用邏輯應用程式可防止 Logic Apps 引擎建立和執行邏輯應用程式未來的工作流程執行個體。 在 Cloud Explorer 中，開啟邏輯應用程式的捷徑功能表，然後選取 [停用]****。

![在 Cloud Explorer 中停用邏輯應用程式](./media/manage-logic-apps-with-visual-studio/disable-logic-app-cloud-explorer.png)

> [!NOTE]
> 當您停用邏輯應用程式後，就不會具現化新的執行。 所有進行中和擱置的執行將會繼續直到完成，這可能需要一些時間。

若要重新開機邏輯應用程式，請在 Cloud Explorer 中，開啟邏輯應用程式的快捷方式功能表，然後選取 [**啟用**]。

![在 Cloud Explorer 中啟用邏輯應用程式](./media/manage-logic-apps-with-visual-studio/enable-logic-app-cloud-explorer.png)

## <a name="delete-your-logic-app"></a>刪除邏輯應用程式

若要從 Azure 入口網站刪除您的邏輯應用程式，請在 Cloud Explorer 中開啟邏輯應用程式的捷徑功能表，然後選取 [刪除]****。

![從 Azure 入口網站刪除邏輯應用程式](./media/manage-logic-apps-with-visual-studio/delete-logic-app-from-azure-portal.png)

> [!NOTE]
> 當您刪除邏輯應用程式後，就不會具現化新的執行。 所有進行中和擱置的執行都會取消。 如果您有數千個執行，加以取消可能需要很長的時間。

> [!NOTE]
> 如果您刪除並重新建立子邏輯應用程式，您必須重新儲存父系邏輯應用程式。 重新建立的子應用程式將會有不同的中繼資料。
> 如果您不在重新建立父邏輯應用程式之後重新儲存其子系，您對子邏輯應用程式的呼叫將會失敗，並出現「未經授權」錯誤。 這種行為適用于父子式邏輯應用程式，例如，使用整合帳戶中的成品或呼叫 Azure 函式的專案。

## <a name="troubleshooting"></a>疑難排解

當您在 Logic Apps 設計工具中開啟您的邏輯應用程式專案時，您可能不會取得選取 Azure 訂用帳戶的選項。 相反地，您的邏輯應用程式以不是您想要使用的 Azure 訂用帳戶開啟。 此行為是因為當您開啟邏輯應用程式的 .json 檔案之後，Visual Studio 會快取第一個選取的訂用帳戶供未來使用。 若要解決此問題，請嘗試下列任一步驟：

* 重新命名邏輯應用程式的 .json 檔案。 訂用帳戶快取是根據檔案名稱。

* 若要移除解決方案中*所有*邏輯應用程式先前選取的訂用帳戶，請刪除方案目錄中隱藏的 Visual Studio 設定資料夾（. vs）。 此位置儲存您的訂用帳戶資訊。

## <a name="next-steps"></a>後續步驟

您已在本文中了解如何使用 Visual Studio 管理已部署的邏輯應用程式。 接下來，請了解如何針對部署自訂邏輯應用程式定義：

> [!div class="nextstepaction"]
> [以 JSON 撰寫邏輯應用程式定義](../logic-apps/logic-apps-author-definitions.md)
