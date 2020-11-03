---
title: 快速入門：使用 ARM 範本部署 Azure API for FHIR
description: 在本快速入門中，了解如何使用 Azure Resource Manager 範本 (ARM 範本)，來部署適用於快速健康照護互通資源 (FHIR®) 的 Azure API。
author: mgblythe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mblythe
ms.date: 10/06/2020
ms.openlocfilehash: 0d045aac63daf99bf67ec79438f55eedd5f200d9
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521436"
---
# <a name="quickstart-use-an-arm-template-to-deploy-azure-api-for-fhir"></a>快速入門：使用 ARM 範本部署 Azure API for FHIR

在本快速入門中，您將了解如何使用 Azure Resource Manager 範本 (ARM 範本)，來部署適用於快速健康照護互通資源 (FHIR®) 的 Azure API。 您可以透過 Azure 入口網站、PowerShell 或 CLI 來部署 Azure API for FHIR。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果您的環境符合必要條件，而且您很熟悉 ARM 範本，請選取 [部署至 Azure] 按鈕。 一旦您登入，範本就會在 Azure 入口網站中開啟。

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="在 Azure 入口網站中使用 ARM 範本，來部署 Azure API for FHIR 服務。":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-api-for-fhir%2fazuredeploy.json)

## <a name="prerequisites"></a>必要條件

# <a name="portal"></a>[入口網站](#tab/azure-portal)

具有有效訂用帳戶的 Azure 帳戶。 [建立免費帳戶](https://azure.microsoft.com/free/)。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* 具有有效訂用帳戶的 Azure 帳戶。 [建立免費帳戶](https://azure.microsoft.com/free/)。
* 如果要在本機執行程式碼，請使用 [Azure PowerShell](/powershell/azure/install-az-ps)。

# <a name="cli"></a>[CLI](#tab/CLI)

* 具有有效訂用帳戶的 Azure 帳戶。 [建立免費帳戶](https://azure.microsoft.com/free/)。
* 如果您想要在本機執行程式碼：
    * Bash shell (例如 Git Bash，其包含在 [Git for Windows](https://gitforwindows.org) 中)。
    * [Azure CLI](/cli/azure/install-azure-cli)。

---

## <a name="review-the-template"></a>檢閱範本

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/101-azure-api-for-fhir/)。

:::code language="json" source="~/quickstart-templates/101-azure-api-for-fhir/azuredeploy.json":::

範本會定義一個 Azure 資源：

* **Microsoft.HealthcareApis/services**

<!--

Replace the line above with the following line once https://docs.microsoft.com/azure/templates/microsoft.healthcareapis/services goes live:

* [**Microsoft.HealthcareApis/services**](/azure/templates/microsoft.healthcareapis/services)

-->

## <a name="deploy-the-template"></a>部署範本

# <a name="portal"></a>[入口網站](#tab/azure-portal)

選取下列連結，以在 Azure 入口網站中使用 ARM 範本，來部署 Azure API for FHIR：

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="在 Azure 入口網站中使用 ARM 範本，來部署 Azure API for FHIR 服務。":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-api-for-fhir%2fazuredeploy.json)

在 [部署 Azure API for FHIR] 頁面上：

1. 如果您想要的話，請將 [訂用帳戶] 從預設值變更為不同的訂用帳戶。

2. 針對 [資源群組]，選取 [新建]，然後輸入新資源群組的名稱並選取 [確認]。

3. 如果您已建立新的資源群組，請選取資源群組的 [區域]。

4. 輸入新的 [服務名稱] ，然後選擇 Azure API for FHIR 的 [位置]。 此位置可以與資源群組的區域相同或不同。

    :::image type="content" source="./media/fhir-paas-arm-template-quickstart/deploy-azure-api-fhir.png" alt-text="在 Azure 入口網站中使用 ARM 範本，來部署 Azure API for FHIR。":::

5. 選取 [檢閱 + 建立]。

6. 閱讀條款及條件，然後選取 [建立]。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

> [!NOTE]
> 如果您想要在本機執行 PowerShell 指令碼，請先輸入 `Connect-AzAccount` 來設定您的 Azure 認證。

如果 `Microsoft.HealthcareApis` 資源提供者尚未針對您的訂用帳戶註冊，您可以使用下列互動式程式碼加以註冊。 若要在 Azure Cloud Shell 中執行程式碼，請在任何程式碼區塊的右上角選取 [試用]。

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.HealthcareApis
```

使用下列程式碼，利用 ARM 範本來部署 Azure API for FHIR 服務。 此程式碼會提示您提供新的 FHIR 服務名稱、新資源群組的名稱，以及其中每一個的位置。

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter a name for the new Azure API for FHIR service"
$serviceLocation = Read-Host -Prompt "Enter an Azure region (for example, westus2) for the service"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group to contain the service"
$resourceGroupRegion = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"

Write-Verbose "New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion" -Verbose
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion
Write-Verbose "Run New-AzResourceGroupDeployment to create an Azure API for FHIR service using an ARM template" -Verbose
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-api-for-fhir/azuredeploy.json `
    -serviceName $serviceName `
    -location $serviceLocation
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

如果 `Microsoft.HealthcareApis` 資源提供者尚未針對您的訂用帳戶註冊，您可以使用下列互動式程式碼加以註冊。 若要在 Azure Cloud Shell 中執行程式碼，請在任何程式碼區塊的右上角選取 [試用]。

```azurecli-interactive
az extension add --name healthcareapis
```

使用下列程式碼，利用 ARM 範本來部署 Azure API for FHIR 服務。 此程式碼會提示您提供新的 FHIR 服務名稱、新資源群組的名稱，以及其中每一個的位置。

```azurecli-interactive
read -p "Enter a name for the new Azure API for FHIR service: " serviceName &&
read -p "Enter an Azure region (for example, westus2) for the service: " serviceLocation &&
read -p "Enter a name for the new resource group to contain the service: " resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group: " resourceGroupRegion &&
params='serviceName='$serviceName' location='$serviceLocation &&
echo "CREATE RESOURCE GROUP:  az group create --name $resourceGroupName --location $resourceGroupRegion" &&
az group create --name $resourceGroupName --location $resourceGroupRegion &&
echo "RUN az deployment group create, which creates an Azure API for FHIR service using an ARM template" &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-api-for-fhir/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

> [!NOTE]
> 部署需要數分鐘的時間才能完成。 記下 Azure API for FHIR 服務和資源群組的名稱，您可以在稍後用來檢閱已部署的資源。

## <a name="review-deployed-resources"></a>檢閱已部署的資源

# <a name="portal"></a>[入口網站](#tab/azure-portal)

請依照下列步驟來查看新 Azure API for FHIR 服務的概觀：

1. 在 [Azure 入口網站](https://portal.azure.com)中，搜尋並選取 [Azure API for FHIR]。

2. 在 FHIR 清單中，選取您的新服務。 新 Azure API for FHIR 服務的 [概觀] 頁面隨即出現。

3. 若要驗證是否已佈建新的 FHIR API 帳戶，請選取 [FHIR 中繼資料端點] 旁的連結，以擷取 FHIR API 功能陳述式。 連結的格式為 `https://<service-name>.azurehealthcareapis.com/metadata`。 如果已佈建帳戶，則會顯示大型 JSON 檔案。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

執行下列互動式程式碼以檢視 Azure API for FHIR 服務的詳細資料。 您必須輸入新服務和資源群組的名稱。

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter the name of your Azure API for FHIR service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new service has been provisioned"

$requestUri="https://" + $serviceName + ".azurehealthcareapis.com/metadata"
$metadata = Invoke-WebRequest -Uri $requestUri
$metadata.RawContent
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

執行下列互動式程式碼以檢視 Azure API for FHIR 服務的詳細資料。 您必須輸入新服務和資源群組的名稱。

```azurecli-interactive
read -p "Enter the name of your Azure API for FHIR service: " serviceName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az healthcareapis service show --resource-group $resourceGroupName --resource-name $serviceName" &&
az healthcareapis service show --resource-group $resourceGroupName --resource-name $serviceName &&
read -p "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new service has been provisioned: " &&
requestUrl='https://'$serviceName'.azurehealthcareapis.com/metadata' &&
curl --url $requestUrl &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="clean-up-resources"></a>清除資源

如果不再需要，請刪除資源群組，這會刪除資源群組中的資源。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 在 [Azure 入口網站](https://portal.azure.com)中，搜尋並選取 [資源群組]。

2. 在 [資源群組] 清單中，選擇資源群組的名稱。

3. 在資源群組的 [概觀] 頁面中，選取 [刪除資源群組]。

4. 在確認對話方塊凹輸入您的資源群組名稱，然後選取 [刪除]。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to delete"
Write-Verbose "Remove-AzResourceGroup -Name $resourceGroupName" -Verbose
Remove-AzResourceGroup -Name $resourceGroupName
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
read -p "Enter the name of the resource group to delete: " resourceGroupName &&
echo "DELETE A RESOURCE GROUP (AND ITS RESOURCES):  az group delete --name $resourceGroupName" &&
az group delete --name $resourceGroupName &&
read -p "Press [ENTER] to continue: "
```

---

如需逐步教學課程，以引導您完成建立 ARM 範本的流程，請參閱：[建立和部署第一個 ARM 範本教學課程](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

## <a name="next-steps"></a>後續步驟

在本快速入門手冊中，您已將 Azure API for FHIR 部署至您的訂用帳戶。 若要在 Azure API for FHIR 中設定其他設定，請繼續進行其他設定操作指南。 如果您已準備好開始使用 Azure API for FHIR，請參閱如何註冊應用程式了解詳細資訊。

>[!div class="nextstepaction"]
>[Azure API for FHIR 中的其他設定](azure-api-for-fhir-additional-settings.md)

>[!div class="nextstepaction"]
>[註冊應用程式概觀](fhir-app-registration.md)
