---
title: 創建或管理 B2B 集成帳戶
description: 創建、連結和管理集成帳戶，以便與 Azure 邏輯應用進行企業集成
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: 083ed0001adb5524c124295eb3bc31f4afad99cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270325"
---
# <a name="create-and-manage-integration-accounts-for-b2b-enterprise-integrations-in-azure-logic-apps"></a>在 Azure 邏輯應用中為 B2B 企業集成創建和管理集成帳戶

若要使用 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 來建置[企業整合和 B2B 解決方案](../logic-apps/logic-apps-enterprise-integration-overview.md)，您必須先建立整合帳戶，這是個別的 Azure 資源，可提供安全、可調整和可管理的容器，以供放置您使用邏輯應用程式工作流程所定義及搭配使用的整合成品。

例如，您可以創建、存儲和管理 B2B 工件，如交易夥伴、協定、地圖、架構、證書和批次處理配置。 此外，在邏輯應用可以使用這些專案並使用邏輯應用 B2B 連接器之前，必須[將集成帳戶連結到](#link-account)邏輯應用。 集成帳戶和邏輯應用必須*存在於同一*位置或區域中。

> [!TIP]
> 要在[整合服務環境中](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)創建集成帳戶，請參閱在[ISE 中創建集成帳戶](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment)。

本主題介紹如何執行這些任務：

* 建立整合帳戶。
* 將整合帳戶連結至邏輯應用程式。
* 更改集成帳戶的定價層。
* 從邏輯應用取消連結集成帳戶。
* 將整合帳戶移到另一個 Azure 資源群組或訂用帳戶。
* 刪除整合帳戶。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

## <a name="create-integration-account"></a>建立整合帳戶

對於此任務，可以通過按照本節中的步驟[Azure PowerShell](/powershell/module/Az.LogicApp/New-AzIntegrationAccount)或[Azure CLI](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-create)使用 Azure 門戶。

1. 使用您的 Azure 帳戶認證登入 [Azure 入口網站](https://portal.azure.com)。

1. 在 Azure 主功能表上，選取 [建立資源]****。 在搜索框中，輸入"集成帳戶"作為篩選器，然後選擇 **"集成帳戶**"。

   ![創建新的集成帳戶](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

1. 在 **"集成帳戶**"下，選擇 **"創建**"。

   ![選擇 [新增] 以建立整合帳戶](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

1. 提供有關集成帳戶的資訊：

   ![提供集成帳戶詳細資訊](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | 屬性 | 必要 | 值 | 描述 |
   |----------|----------|-------|-------------|
   | **名稱** | 是 | <*集成帳戶名稱*> | 集成帳戶的名稱，只能包含字母、數位、連字號`-`（）、底線 （）、`_`括弧 （`(`和`)`期間 （`.`）。 此示例使用"法布裡卡姆集成"。 |
   | **訂閱** | 是 | <*Azure 訂閱名稱*> | Azure 訂用帳戶的名稱 |
   | **資源組** | 是 | <*Azure 資源組名稱*> | 用於組織相關[資源的 Azure 資源組](../azure-resource-manager/management/overview.md)的名稱。 在此示例中，創建一個名稱為"Fabrikam 集成-RG"的新資源組。 |
   | **定價層** | 是 | <*定價級別*> | 集成帳戶的定價層，您可以稍後更改該層。 在此示例中，選擇 **"免費**"。 如需詳細資訊，請參閱下列主題： <p>- [邏輯應用定價模型](../logic-apps/logic-apps-pricing.md#integration-accounts) <p>- [邏輯應用限制和配置](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) <p>- [邏輯應用定價](https://azure.microsoft.com/pricing/details/logic-apps/) |
   | **位置** | 是 | <*Azure 區域*> | 存儲集成帳戶中繼資料的區域。 選擇與邏輯應用相同的位置，或者在與集成帳戶相同的位置創建邏輯應用。 在此示例中，請使用"美國西部"。 <p>**注意**：要在[整合服務環境 （ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中創建集成帳戶，請選擇該 ISE 作為位置。 有關詳細資訊，請參閱在[ISE 中創建集成帳戶](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment)。 |
   | **日誌分析** | 否 | 關，開 | 保留此示例的 **"關閉**"設置。 |
   |||||

1. 完成後，選擇 **"創建**"。

   部署完成後，Azure 將打開集成帳戶。

   ![Azure 打開集成帳戶](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

1. 在邏輯應用可以使用集成帳戶之前，請按照後續步驟將集成帳戶和邏輯應用連結在一起。

<a name="link-account"></a>

## <a name="link-to-logic-app"></a>連結至邏輯應用程式

要授予邏輯應用對包含 B2B 專案的功能的集成帳戶的存取權限，必須首先將集成帳戶連結到邏輯應用。 邏輯應用和集成帳戶必須存在於同一區域中。 要完成此任務，可以使用 Azure 門戶。 如果使用 Visual Studio，並且邏輯應用位於[Azure 資源組專案中](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)，則可以使用[Visual Studio 將邏輯應用連結到集成帳戶](../logic-apps/manage-logic-apps-with-visual-studio.md#link-integration-account)。

1. 在 Azure 入口網站中，尋找並開啟邏輯應用程式。

1. 在[Azure 門戶](https://portal.azure.com)中，打開現有邏輯應用或創建新的邏輯應用。

1. 在邏輯應用程式功能表的 [設定]**** 底下，選取 [工作流程設定]****。 在 **"集成帳戶**"下，打開 **"選擇集成帳戶**"清單。 選擇要連結到邏輯應用的集成帳戶。

   ![選取您的整合帳戶](./media/logic-apps-enterprise-integration-create-integration-account/select-integration-account.png)

1. 要完成連結，請選擇"**保存**"。

   ![選取您的整合帳戶](./media/logic-apps-enterprise-integration-create-integration-account/save-link.png)

   成功連結集成帳戶後，Azure 將顯示一條確認訊息。

   ![Azure 會確認成功的連結](./media/logic-apps-enterprise-integration-create-integration-account/link-confirmation.png)

現在，邏輯應用可以使用集成帳戶中的工件以及 B2B 連接器，如 XML 驗證和一般檔案編碼或解碼。  

<a name="change-pricing-tier"></a>

## <a name="change-pricing-tier"></a>變更定價層

要增加集成[帳戶的限制，](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)可以[升級到更高的定價層](#upgrade-pricing-tier)（如果可用）。 例如，可以從免費層升級到基本層或標準層。 您也可以[降級到較低層](#downgrade-pricing-tier)（如果可用）。 有關詳細資訊定價資訊，請參閱以下主題：

* [邏輯應用定價](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Logic Apps 的定價模式](../logic-apps/logic-apps-pricing.md#integration-accounts)

<a name="upgrade-pricing-tier"></a>

### <a name="upgrade-pricing-tier"></a>升級定價層

要進行此更改，可以使用 Azure 門戶，請按照本節中的步驟或[Azure CLI](#upgrade-tier-azure-cli)。

#### <a name="azure-portal"></a>Azure 入口網站

1. 使用您的 Azure 帳戶認證登入 [Azure 入口網站](https://portal.azure.com)。

1. 在主 Azure 搜索框中，輸入"集成帳戶"作為篩選器，然後選擇 **"集成帳戶**"。

   ![尋找整合帳戶](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure 顯示 Azure 訂閱中的所有集成帳戶。

1. 在 [整合帳戶]**** 底下，選取您想要移動的整合帳戶。 在集成帳戶功能表上，選擇 **"概述**"。

   ![在集成帳戶功能表上，選擇"概述"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. 在"概述"窗格中，選擇 **"升級定價層**"，其中列出了任何可用的較高層。 選擇層時，更改將立即生效。

<a name="upgrade-tier-azure-cli"></a>

#### <a name="azure-cli"></a>Azure CLI

1. 如果尚未執行此操作，[請安裝 Azure CLI 先決條件](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)。

1. 在 Azure 門戶中，打開 Azure[**雲外殼**](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)環境。

   ![開啟 Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. 在命令提示符處，輸入[**az 資源**命令](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-update)，`skuName`並設置為所需的較高層。

   ```azurecli
   az resource update --resource-group {ResourceGroupName} --resource-type Microsoft.Logic/integrationAccounts --name {IntegrationAccountName} --subscription {AzureSubscriptionID} --set sku.name={SkuName}
   ```
  
   例如，如果您有基本層，則可以設置為`skuName``Standard`：

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Standard
   ```

<a name="downgrade-pricing-tier"></a>

### <a name="downgrade-pricing-tier"></a>降級定價層

要進行此更改，請使用[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)。

1. 如果尚未執行此操作，[請安裝 Azure CLI 先決條件](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)。

1. 在 Azure 門戶中，打開 Azure[**雲外殼**](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)環境。

   ![開啟 Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. 在命令提示符處，輸入[**az 資源**命令](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-update)並將其設置為`skuName`所需的下層。

   ```azurecli
   az resource update --resource-group <resourceGroupName> --resource-type Microsoft.Logic/integrationAccounts --name <integrationAccountName> --subscription <AzureSubscriptionID> --set sku.name=<skuName>
   ```
  
   例如，如果您有標準層，則可以設置為`skuName``Basic`：

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Basic
   ```

## <a name="unlink-from-logic-app"></a>從邏輯應用程式取消連結

如果要將邏輯應用連結到其他集成帳戶，或者不再使用邏輯應用的集成帳戶，請使用 Azure 資源資源管理器刪除連結。

1. 打開瀏覽器視窗，然後轉到[Azure 資源資源管理器https://resources.azure.com)（。](https://resources.azure.com) 使用相同的 Azure 帳戶憑據登錄。

   ![Azure 資源總管](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

1. 在搜索框中，輸入邏輯應用的名稱，以便找到並選擇邏輯應用。

   ![尋找並選取邏輯應用程式](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

1. 在資源管理器標題列上，選擇 **"讀/寫**"。

   ![開啟「讀取/寫入」模式](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-read-write.png)

1. 在"**資料**"選項卡上，選擇 **"編輯**"。

   ![在"資料"選項卡上，選擇"編輯"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-edit.png)

1. 在編輯器中，查找`integrationAccount`物件，並刪除具有以下格式的屬性：

   ```json
   {
      // <other-attributes>
      "integrationAccount": {
         "name": "<integration-account-name>",
         "id": "<integration-account-resource-ID>",
         "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   例如：

   ![查找"集成帳戶"物件](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

1. 在"**資料**"選項卡上，選擇 **"放置"** 以保存更改。

   ![要保存更改，請選擇"放置"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

1. 在 Azure 入口網站中，尋找並選取邏輯應用程式。 在應用的 **"工作流設置**"下，檢查 **"集成"帳戶**屬性現在顯示為空。

   ![確認整合帳戶未連結](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>移動整合帳戶

您可以將集成帳戶移動到其他 Azure 資源組或 Azure 訂閱。 移動資源時，Azure 會創建新的資源標識，因此請確保改用新標識，並更新與移動資源關聯的任何腳本或工具。 如果要更改訂閱，還必須指定現有資源組或新資源組。

對於此任務，可以通過按照本節中的步驟或[Azure CLI](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-move)使用 Azure 門戶。

1. 使用您的 Azure 帳戶認證登入 [Azure 入口網站](https://portal.azure.com)。

1. 在主 Azure 搜索框中，輸入"集成帳戶"作為篩選器，然後選擇 **"集成帳戶**"。

   ![尋找整合帳戶](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure 顯示 Azure 訂閱中的所有集成帳戶。

1. 在 [整合帳戶]**** 底下，選取您想要移動的整合帳戶。 在集成帳戶功能表上，選擇 **"概述**"。

   ![在集成帳戶功能表上，選擇"概述"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. 在**資源組**或**訂閱名稱**旁邊，選擇**更改**。

   ![更改資源組或訂閱](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

1. 選擇您也要移動的任何相關資源。

1. 根據您的選擇，按照以下步驟更改資源組或訂閱：

   * 資源組：從 **"資源組"** 清單中，選擇目標資源組。 或者，要創建其他資源組，請選擇"**創建新資源組**"。

   * 訂閱：從**訂閱**清單中選擇目標訂閱。 從 **"資源組"** 清單中，選擇目標資源組。 或者，要創建其他資源組，請選擇"**創建新資源組**"。

1. 要確認您的理解，即與移動資源關聯的任何腳本或工具在使用新資源指示更新它們之前都不起作用，請選擇確認框，然後選擇"**確定**"。

1. 完成後，請確保使用移動資源的新資源 I 更新任何和所有腳本。  

## <a name="delete-integration-account"></a>刪除整合帳戶

對於此任務，可以通過按照本節中的步驟[Azure CLI](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-delete)或[Azure PowerShell](/powershell/module/az.logicapp/remove-azintegrationaccount)使用 Azure 門戶。

1. 使用您的 Azure 帳戶認證登入 [Azure 入口網站](https://portal.azure.com)。

1. 在主 Azure 搜索框中，輸入"集成帳戶"作為篩選器，然後選擇 **"集成帳戶**"。

   ![尋找整合帳戶](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure 顯示 Azure 訂閱中的所有集成帳戶。

1. 在 [整合帳戶]**** 底下，選取您想要刪除的整合帳戶。 在集成帳戶功能表上，選擇 **"概述**"。

   ![在集成帳戶功能表上，選擇"概述"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. 在"概述"窗格中，選擇 **"刪除**"。

   ![在"概述"窗格中，選擇"刪除"](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

1. 要確認要刪除集成帳戶，請選擇"**是**"。

   ![要確認刪除，請選擇"是"](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>後續步驟

* [在您的集成帳戶中創建交易夥伴](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [在集成帳戶中的合作夥伴之間創建協定](../logic-apps/logic-apps-enterprise-integration-agreements.md)
