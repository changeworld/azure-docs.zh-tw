---
title: 建立或管理 B2B 整合帳戶
description: 建立、連結及管理整合帳戶以進行企業與 Azure Logic Apps 的整合
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: 083ed0001adb5524c124295eb3bc31f4afad99cf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84691752"
---
# <a name="create-and-manage-integration-accounts-for-b2b-enterprise-integrations-in-azure-logic-apps"></a>在 Azure Logic Apps 中建立和管理供 B2B 企業整合運用的整合帳戶

若要使用 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 來建置[企業整合和 B2B 解決方案](../logic-apps/logic-apps-enterprise-integration-overview.md)，您必須先建立整合帳戶，這是個別的 Azure 資源，可提供安全、可調整和可管理的容器，以供放置您使用邏輯應用程式工作流程所定義及搭配使用的整合成品。

例如，您可以建立、儲存和管理 B2B 成品，例如交易夥伴、合約、地圖、架構、憑證及批次設定。 此外，您必須先將[整合帳戶連結](#link-account)至邏輯應用程式，您的邏輯應用程式才能使用這些成品，並使用 Logic Apps B2B 連接器。 您的整合帳戶和邏輯應用程式都必須存在於*相同*的位置或區域。

> [!TIP]
> 若要在[整合服務環境](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)內建立整合帳戶，請參閱[在 ISE 中建立整合帳戶](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment)。

本主題說明如何執行下列工作：

* 建立整合帳戶。
* 將整合帳戶連結至邏輯應用程式。
* 變更整合帳戶的定價層。
* 將您的整合帳戶與邏輯應用程式取消連結。
* 將整合帳戶移到另一個 Azure 資源群組或訂用帳戶。
* 刪除整合帳戶。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

## <a name="create-integration-account"></a>建立整合帳戶

針對這項工作，您可以依照本節中的步驟， [Azure PowerShell](/powershell/module/Az.LogicApp/New-AzIntegrationAccount)或[Azure CLI](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-create)來使用 Azure 入口網站。

1. 使用您的 Azure 帳戶認證登入 [Azure 入口網站](https://portal.azure.com)。

1. 在 Azure 主功能表上，選取 [建立資源]****。 在搜尋方塊中，輸入「整合帳戶」作為篩選準則，然後選取 [**整合帳戶**]。

   ![建立新的整合帳戶](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

1. 在 [**整合帳戶**] 底下，選取 [**建立**]。

   ![選擇 [新增] 以建立整合帳戶](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

1. 提供您整合帳戶的下列資訊：

   ![提供整合帳戶詳細資料](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | 屬性 | 必要 | 值 | 描述 |
   |----------|----------|-------|-------------|
   | **名稱** | Yes | <*整合-帳戶-名稱*> | 您的整合帳戶名稱，其中只能包含字母、數位、連字號（ `-` ）、底線（ `_` ）、括弧（ `(` ， `)` ）和句點（ `.` ）。 這個範例會使用「Fabrikam-整合」。 |
   | **訂用帳戶** | Yes | <*Azure-subscription-name*> | Azure 訂用帳戶的名稱 |
   | **資源群組** | Yes | <*Azure-resource-group-name*> | 用來組織相關資源之[Azure 資源群組](../azure-resource-manager/management/overview.md)的名稱。 針對此範例，請建立名為 "FabrikamIntegration-RG" 的新資源群組。 |
   | 定價層 | Yes | <*定價層級*> | 整合帳戶的定價層，您可于稍後變更。 針對此範例，請選取 [**免費**]。 如需詳細資訊，請參閱下列主題： <p>- [Logic Apps 計價模式](../logic-apps/logic-apps-pricing.md#integration-accounts) <p>- [Logic Apps 限制和設定](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) <p>- [Logic Apps 定價](https://azure.microsoft.com/pricing/details/logic-apps/) |
   | **位置** | 是 | <*Azure-region*> | 要儲存整合帳戶中繼資料的區域。 請選取與邏輯應用程式相同的位置，或在與整合帳戶相同的位置中建立邏輯應用程式。 在此範例中，請使用「美國西部」。 <p>**注意**：若要在[整合服務環境（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)內建立整合帳戶，請選取該 ISE 作為位置。 如需詳細資訊，請參閱[在 ISE 中建立整合帳戶](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment)。 |
   | **Log Analytics** | No | Off、On | 針對此範例，請保留 [**關閉**] 設定。 |
   |||||

1. 完成之後，選取 [建立]。

   部署完成之後，Azure 會開啟您的整合帳戶。

   ![Azure 會開啟整合帳戶](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

1. 在您的邏輯應用程式可以使用您的整合帳戶之前，請遵循後續步驟，將整合帳戶和邏輯應用程式連結在一起。

<a name="link-account"></a>

## <a name="link-to-logic-app"></a>連結至邏輯應用程式

若要將您的邏輯應用程式存取權授與包含 B2B 成品的整合帳戶，您必須先將整合帳戶連結到邏輯應用程式。 邏輯應用程式和整合帳戶必須存在於相同的區域中。 若要完成這項工作，您可以使用 Azure 入口網站。 如果您使用 Visual Studio，而您的邏輯應用程式是在[Azure 資源群組專案](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)中，您可以[使用 Visual Studio 將邏輯應用程式連結至整合帳戶](../logic-apps/manage-logic-apps-with-visual-studio.md#link-integration-account)。

1. 在 Azure 入口網站中，尋找並開啟邏輯應用程式。

1. 在[Azure 入口網站](https://portal.azure.com)中，開啟現有的邏輯應用程式，或建立新的邏輯應用程式。

1. 在邏輯應用程式功能表的 [設定] 底下，選取 [工作流程設定]。 在 [**整合帳戶**] 下，開啟 [**選取整合帳戶**] 清單。 選取整合帳戶以連結至您的邏輯應用程式。

   ![選取您的整合帳戶](./media/logic-apps-enterprise-integration-create-integration-account/select-integration-account.png)

1. 若要完成連結，請選取 [**儲存**]。

   ![選取您的整合帳戶](./media/logic-apps-enterprise-integration-create-integration-account/save-link.png)

   成功連結整合帳戶之後，Azure 會顯示確認訊息。

   ![Azure 會確認成功的連結](./media/logic-apps-enterprise-integration-create-integration-account/link-confirmation.png)

現在，您的邏輯應用程式可以使用整合帳戶中的構件加上 B2B 連接器，例如 XML 驗證和一般檔案編碼或解碼。  

<a name="change-pricing-tier"></a>

## <a name="change-pricing-tier"></a>變更定價層

若要增加整合帳戶的[限制](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)，您可以[升級至較高的定價層](#upgrade-pricing-tier)（如果有的話）。 例如，您可以從免費層升級至基本層或標準層。 您也可以[降級為較低層](#downgrade-pricing-tier)（如果有的話）。 如需定價資訊的詳細資訊，請參閱下列主題：

* [Logic Apps 定價](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Logic Apps 的定價模式](../logic-apps/logic-apps-pricing.md#integration-accounts)

<a name="upgrade-pricing-tier"></a>

### <a name="upgrade-pricing-tier"></a>升級定價層

若要進行這項變更，您可以遵循本節中的步驟或[Azure CLI](#upgrade-tier-azure-cli)，使用 Azure 入口網站。

#### <a name="azure-portal"></a>Azure 入口網站

1. 使用您的 Azure 帳戶認證登入 [Azure 入口網站](https://portal.azure.com)。

1. 在主要 [Azure 搜尋服務] 方塊中，輸入「整合帳戶」作為篩選準則，然後選取 [**整合帳戶**]。

   ![尋找整合帳戶](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure 會顯示您 Azure 訂用帳戶中的所有整合帳戶。

1. 在 [整合帳戶]**** 底下，選取您想要移動的整合帳戶。 在您的 [整合帳戶] 功能表上，選取 **[總覽**]。

   ![在 [整合帳戶] 功能表上，選取 [總覽]](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. 在 [總覽] 窗格中，選取 [**升級定價層**]，其中會列出任何可用的較高層級。 當您選取一層時，變更會立即生效。

<a name="upgrade-tier-azure-cli"></a>

#### <a name="azure-cli"></a>Azure CLI

1. 如果您尚未這麼做，請[安裝 Azure CLI 必要條件](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)。

1. 在 [Azure 入口網站中，開啟 Azure [**Cloud Shell**](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)環境。

   ![開啟 Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. 在命令提示字元中，輸入[ **az resource**命令](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-update)，並將設定 `skuName` 為您想要的較高層級。

   ```azurecli
   az resource update --resource-group {ResourceGroupName} --resource-type Microsoft.Logic/integrationAccounts --name {IntegrationAccountName} --subscription {AzureSubscriptionID} --set sku.name={SkuName}
   ```
  
   例如，如果您有基本層，您可以將設定 `skuName` 為 `Standard` ：

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Standard
   ```

<a name="downgrade-pricing-tier"></a>

### <a name="downgrade-pricing-tier"></a>降級定價層

若要進行此變更，請使用[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)。

1. 如果您尚未這麼做，請[安裝 Azure CLI 必要條件](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)。

1. 在 [Azure 入口網站中，開啟 Azure [**Cloud Shell**](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)環境。

   ![開啟 Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. 在命令提示字元中，輸入[ **az resource**命令](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-update)，並將設定 `skuName` 為您想要的較低層。

   ```azurecli
   az resource update --resource-group <resourceGroupName> --resource-type Microsoft.Logic/integrationAccounts --name <integrationAccountName> --subscription <AzureSubscriptionID> --set sku.name=<skuName>
   ```
  
   例如，如果您有標準層，您可以將設定 `skuName` 為 `Basic` ：

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Basic
   ```

## <a name="unlink-from-logic-app"></a>從邏輯應用程式取消連結

如果您想要將邏輯應用程式連結至另一個整合帳戶，或不再將整合帳戶與邏輯應用程式搭配使用，請使用 Azure 資源總管刪除連結。

1. 開啟瀏覽器視窗，然後移至[Azure 資源總管（ https://resources.azure.com) ](https://resources.azure.com)）。 使用相同的 Azure 帳號憑證登入。

   ![Azure 資源總管](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

1. 在 [搜尋] 方塊中，輸入邏輯應用程式的名稱，讓您可以尋找並選取您的邏輯應用程式。

   ![尋找並選取邏輯應用程式](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

1. 在 explorer 標題列上，選取 [**讀取/寫入**]。

   ![開啟「讀取/寫入」模式](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-read-write.png)

1. 在 [**資料**] 索引標籤上，選取 [**編輯**]。

   ![在 [資料] 索引標籤上，選取 [編輯]](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-edit.png)

1. 在編輯器中，尋找 `integrationAccount` 物件，並刪除該屬性，其格式如下：

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

   ![尋找 "integrationAccount" 物件](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

1. 在 [**資料**] 索引標籤上，選取 [ **Put** ] 以儲存變更。

   ![若要儲存變更，請選取 [Put]](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

1. 在 Azure 入口網站中，尋找並選取邏輯應用程式。 在您應用程式的**工作流程設定**底下，檢查**整合帳戶**屬性現在是否顯示為空白。

   ![確認整合帳戶未連結](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>移動整合帳戶

您可以將整合帳戶移至另一個 Azure 資源群組或 Azure 訂用帳戶。 當您移動資源時，Azure 會建立新的資源識別碼，因此請務必改為使用新的識別碼，並更新與已移動資源相關聯的任何腳本或工具。 如果您想要變更訂用帳戶，您也必須指定現有或新的資源群組。

針對這項工作，您可以依照本節中的步驟或[Azure CLI](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-move)使用 Azure 入口網站。

1. 使用您的 Azure 帳戶認證登入 [Azure 入口網站](https://portal.azure.com)。

1. 在主要 [Azure 搜尋服務] 方塊中，輸入「整合帳戶」作為篩選準則，然後選取 [**整合帳戶**]。

   ![尋找整合帳戶](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure 會顯示您 Azure 訂用帳戶中的所有整合帳戶。

1. 在 [整合帳戶]**** 底下，選取您想要移動的整合帳戶。 在您的 [整合帳戶] 功能表上，選取 **[總覽**]。

   ![在 [整合帳戶] 功能表上，選取 [總覽]](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. 在 [**資源群組**] 或 [訂用帳戶**名稱**] 旁，選取 [**變更**]。

   ![變更資源群組或訂用帳戶](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

1. 選取您也想要移動的任何相關資源。

1. 根據您的選擇，遵循下列步驟來變更資源群組或訂用帳戶：

   * 資源群組：從 [**資源群組**] 清單中，選取目的地資源群組。 或者，若要建立不同的資源群組，請選取 [**建立新的資源群組**]。

   * 訂用帳戶：從 [**訂**用帳戶] 清單中，選取目的地訂用帳戶。 從 [**資源群組**] 清單中，選取目的地資源群組。 或者，若要建立不同的資源群組，請選取 [**建立新的資源群組**]。

1. 若要確認您的瞭解，與所移動資源相關聯的任何腳本或工具都將無法使用，直到您使用新的資源識別碼更新它們為止，請選取 [確認] 方塊，然後選取 **[確定]**。

1. 完成之後，請確定您已使用已移動資源的新資源識別碼來更新任何和所有腳本。  

## <a name="delete-integration-account"></a>刪除整合帳戶

針對這項工作，您可以依照本節中的步驟， [Azure CLI](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-delete)或[Azure PowerShell](/powershell/module/az.logicapp/remove-azintegrationaccount)來使用 Azure 入口網站。

1. 使用您的 Azure 帳戶認證登入 [Azure 入口網站](https://portal.azure.com)。

1. 在主要 [Azure 搜尋服務] 方塊中，輸入「整合帳戶」作為篩選準則，然後選取 [**整合帳戶**]。

   ![尋找整合帳戶](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure 會顯示您 Azure 訂用帳戶中的所有整合帳戶。

1. 在 [整合帳戶]**** 底下，選取您想要刪除的整合帳戶。 在您的 [整合帳戶] 功能表上，選取 **[總覽**]。

   ![在 [整合帳戶] 功能表上，選取 [總覽]](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. 在 [總覽] 窗格中，選取 [**刪除**]。

   ![在 [總覽] 窗格中，選取 [刪除]](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

1. 若要確認您想要刪除整合帳戶，請選取 **[是]**。

   ![若要確認刪除，請選取 [是]](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>後續步驟

* [在您的整合帳戶中建立交易夥伴](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [在整合帳戶中的合作夥伴之間建立協定](../logic-apps/logic-apps-enterprise-integration-agreements.md)
