---
title: 快速入門：建立 Azure SignalR 服務 - ARM 範本
description: 在本快速入門中，您會了解如何使用 Azure Resource Manager 範本 (ARM 範本) 建立 Azure SignalR 服務。
author: sffamily
ms.service: signalr
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
ms.author: zhshang
ms.date: 10/02/2020
ms.openlocfilehash: 4ab029048b37a4dcb44ef405249dcb9e20de70cf
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94841610"
---
# <a name="quickstart-use-an-arm-template-to-deploy-azure-signalr-service"></a>快速入門：使用 ARM 範本來部署 Azure SignalR 服務

本快速入門說明如何使用 Azure Resource Manager 範本 (ARM 範本) 建立 Azure SignalR 服務。 您可以透過 Azure 入口網站、PowerShell 或 CLI 來部署 Azure SignalR 服務。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果您的環境符合必要條件，而且您很熟悉 ARM 範本，請選取 [部署至 Azure] 按鈕。 一旦您登入，範本就會在 Azure 入口網站中開啟。

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="在 Azure 入口網站中使用 ARM 範本來部署 Azure SignalR 服務的按鈕。":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-signalr%2fazuredeploy.json)

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

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/101-signalr/)。

:::code language="json" source="~/quickstart-templates/101-signalr/azuredeploy.json":::

範本會定義一個 Azure 資源：

* [**Microsoft.SignalRService/SignalR**](/azure/templates/microsoft.signalrservice/signalr)

## <a name="deploy-the-template"></a>部署範本

# <a name="portal"></a>[入口網站](#tab/azure-portal)

選取下列連結，以在 Azure 入口網站中使用 ARM 範本來部署 Azure SignalR 服務：

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="在 Azure 入口網站中使用 ARM 範本來部署 Azure SignalR 服務的按鈕。":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-signalr%2fazuredeploy.json)

在 [部署 Azure SignalR 服務] 頁面上：

1. 如果您想要，請從預設值變更 [訂用帳戶]。

2. 針對 [資源群組]，選取 [新建]，然後輸入新資源群組的名稱並選取 [確認]。

3. 如果您已建立新的資源群組，請選取資源群組的 [區域]。

4. 如果您想要，請輸入 Azure SignalR 服務的新 [名稱] 和 [位置] (例如 **eastus2**)。 如果您未指定名稱，則會自動產生。 Azure SignalR 服務的位置可以與資源群組的區域相同或不同。 如果您未指定位置，則會將其設定為與資源群組相同的區域。

5. 選擇 [定價層] (**Free_F1** 或 **Standard_S1**)，輸入 [容量] (SignalR 單位數)，然後選擇 [預設] (需要中樞伺服器)、[無伺服器] (不允許任何伺服器連線) 或 [傳統](只有在中樞具有伺服器連線時才會路由傳送至中樞伺服器) 作為 [服務模式]。 然後選擇是否 [啟用連線記錄] 或 [啟用傳訊記錄]。

    > [!NOTE]
    > 若為 [Free_F1] 定價層，容量限制為 1 單位。

    :::image type="content" source="./media/signalr-quickstart-azure-signalr-service-arm-template/deploy-azure-signalr-service-arm-template-portal.png" alt-text="用於在 Azure 入口網站中建立 Azure SignalR 服務之 ARM 範本的螢幕擷取畫面。":::

6. 選取 [檢閱 + 建立]。

7. 閱讀條款及條件，然後選取 [建立]。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

> [!NOTE]
> 如果您想要在本機執行 PowerShell 指令碼，請先輸入 `Connect-AzAccount` 來設定您的 Azure 認證。

使用下列程式碼，利用 ARM 範本來部署 Azure SignalR 服務。 程式碼會提示您提供下列項目：

* 新 Azure SignalR 服務的名稱和區域
* 新資源群組的名稱和區域
* Azure 定價層 (**Free_F1** 或 **Standard_S1**)
* SignalR 單位容量 (1、2、5、10、20、50 或 100)
  > [!NOTE]
  > 若為 [Free_F1] 定價層，容量限制為 1 單位。
* 服務模式：[預設] 需要中樞伺服器、[無伺服器] 不允許任何伺服器連線，或 [傳統] 只有在中樞具有伺服器連線時才會路由傳送至中樞伺服器
* 是否要啟用連線或傳訊的記錄 (**true** 或 **false**)

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter a name for the new Azure SignalR Service"
$serviceLocation = Read-Host -Prompt "Enter an Azure region (for example, westus2) for the service"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group to contain the service"
$resourceGroupRegion = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"

$priceTier = Read-Host -Prompt "Enter the pricing tier (Free_F1 or Standard_S1)"
$unitCapacity = Read-Host -Prompt "Enter the number of SignalR units (1, 2, 5, 10, 20, 50, or 100)"
$servicingMode = Read-Host -Prompt "Enter the service mode (Default, Serverless, or Classic)"
$enableConnectionLogs = Read-Host -Prompt "Specify whether to enable connectivity logs (true or false)"
$enableMessageLogs = Read-Host -Prompt "Specify whether to enable messaging logs (true or false)"

Write-Verbose "New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion" -Verbose
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion

$paramObjHashTable = @{
    name = $serviceName
    location = $serviceLocation
    pricingTier = $priceTier
    capacity = [int]$unitCapacity
    serviceMode = $servicingMode
    enableConnectivityLogs = $enableConnectionLogs
    enableMessagingLogs = $enableMessageLogs
}

Write-Verbose "Run New-AzResourceGroupDeployment to create an Azure SignalR Service using an ARM template" -Verbose
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateParameterObject $paramObjHashTable `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-signalr/azuredeploy.json
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

使用下列程式碼，利用 ARM 範本來部署 Azure SignalR 服務。 程式碼會提示您提供下列項目：

* 新 Azure SignalR 服務的名稱和區域
* 新資源群組的名稱和區域
* Azure 定價層 (**Free_F1** 或 **Standard_S1**)
* SignalR 單位容量 (1、2、5、10、20、50 或 100)
    > [!NOTE]
    > 若為 [Free_F1] 定價層，容量限制為 1 單位。
* 服務模式：[預設] 需要中樞伺服器、[無伺服器] 不允許任何伺服器連線，或 [傳統] 只有在中樞具有伺服器連線時才會路由傳送至中樞伺服器
* 是否要啟用連線或傳訊的記錄 (**true** 或 **false**)

```azurecli-interactive
read -p "Enter a name for the new Azure SignalR Service: " serviceName &&
read -p "Enter an Azure region (for example, westus2) for the service: " serviceLocation &&
read -p "Enter a name for the new resource group to contain the service: " resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group: " resourceGroupRegion &&
read -p "Enter the pricing tier (Free_F1 or Standard_S1): " priceTier &&
read -p "Enter the number of SignalR units (1, 2, 5, 10, 20, 50, or 100): " unitCapacity &&
read -p "Enter the service mode (Default, Serverless, or Classic): " servicingMode &&
read -p "Specify whether to enable connectivity logs (true or false): " enableConnectionLogs &&
read -p "Specify whether to enable messaging logs (true or false): " enableMessageLogs &&
params='name='$serviceName' location='$serviceLocation' pricingTier='$priceTier' capacity='$unitCapacity' serviceMode='$servicingMode' enableConnectivityLogs='$enableConnectionLogs' enableMessagingLogs='$enableMessageLogs &&
echo "CREATE RESOURCE GROUP:  az group create --name $resourceGroupName --location $resourceGroupRegion" &&
az group create --name $resourceGroupName --location $resourceGroupRegion &&
echo "RUN az deployment group create, which creates an Azure SignalR Service using an ARM template" &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-signalr/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

> [!NOTE]
> 部署需要幾分鐘的時間才能完成。 記下 Azure SignalR 服務和資源群組的名稱，您可以在稍後用來檢閱已部署的資源。

## <a name="review-deployed-resources"></a>檢閱已部署的資源

# <a name="portal"></a>[入口網站](#tab/azure-portal)

請依照下列步驟來查看新 Azure SignalR 服務的概觀：

1. 在 [Azure 入口網站](https://portal.azure.com)中，搜尋並選取 [SignalR]。

2. 在 FHIR 清單中，選取您的新服務。 新 Azure SignalR 服務的 [概觀] 頁面隨即出現。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

執行下列互動式程式碼以檢視 Azure SignalR 服務的詳細資料。 您必須輸入新服務和資源群組的名稱。

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter the name of your Azure SignalR Service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzSignalR -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzSignalR -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

執行下列互動式程式碼以檢視 Azure SignalR 服務的詳細資料。 您必須輸入新服務和資源群組的名稱。

```azurecli-interactive
read -p "Enter the name of your Azure SignalR Service: " serviceName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az signalr show --resource-group $resourceGroupName --name $serviceName" &&
az signalr show --resource-group $resourceGroupName --name $serviceName &&
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

## <a name="next-steps"></a>後續步驟

如需逐步教學課程，以引導您完成建立 ARM 範本的流程，請參閱：

> [!div class="nextstepaction"]
> [教學課程：建立及部署您的第一個 ARM 範本](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
