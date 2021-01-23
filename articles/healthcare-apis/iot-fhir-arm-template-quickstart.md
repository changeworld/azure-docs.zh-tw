---
title: 快速入門：使用 ARM 範本部署適用于 FHIR (preview) 的 Azure IoT Connector
description: 在本快速入門中，您將瞭解如何使用 Azure Resource Manager 範本 (ARM 範本) ，部署適用于 FHIR (preview) 的 Azure IoT Connector。
author: rbhaiya
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: quickstart
ms.author: rabhaiya
ms.date: 01/21/2021
ms.openlocfilehash: b45c63031596c96886a96a21bf693803088cc006
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98745219"
---
# <a name="quickstart-use-an-azure-resource-manager-arm-template-to-deploy-azure-iot-connector-for-fhir-preview"></a>快速入門：使用 Azure Resource Manager (ARM) 範本來部署適用于 FHIR (preview 的 Azure IoT Connector) 

在本快速入門中，您將瞭解如何使用 (ARM 範本的 Azure Resource Manager 範本) 來部署適用于快速健康照護互通資源 (FHIR&#174;) * 功能 Azure API for FHIR 的 Azure IoT Connector。 若要部署適用于 FHIR 的 Azure IoT Connector 的工作實例，此範本也會部署父 Azure API for FHIR 服務和 Azure IoT Central 應用程式，以將遙測從裝置模擬器匯出至 Azure IoT Connector 以進行 FHIR。 您可以執行 ARM 範本，透過 Azure 入口網站、PowerShell 或 CLI 來部署適用于 FHIR 的 Azure IoT Connector。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果您的環境符合必要條件，而且您很熟悉 ARM 範本，請選取 [部署至 Azure] 按鈕。 一旦您登入，範本就會在 Azure 入口網站中開啟。

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="使用 Azure 入口網站中的 ARM 範本，將 Azure IoT Connector for FHIR 部署到 Azure。":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmicrosoft%2Fiomt-fhir%2Fmaster%2Fdeploy%2Ftemplates%2Fmanaged%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisites

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

此 [範本](https://raw.githubusercontent.com/microsoft/iomt-fhir/master/deploy/templates/managed/azuredeploy.json) 會定義下列 Azure 資源：

* **Microsoft.HealthcareApis/services**
* **HealthcareApis/services/iomtconnectors**
* **Microsoft.iotcentral/IoTApps**

## <a name="deploy-the-template"></a>部署範本

# <a name="portal"></a>[入口網站](#tab/azure-portal)

選取下列連結，以使用 Azure 入口網站中的 ARM 範本部署適用于 FHIR 的 Azure IoT Connector：

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="使用 Azure 入口網站中的 ARM 範本，部署至 Azure IoT Connector for FHIR service。":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmicrosoft%2Fiomt-fhir%2Fmaster%2Fdeploy%2Ftemplates%2Fmanaged%2Fazuredeploy.json)

在 [部署 Azure API for FHIR] 頁面上：

1. 如果您想要的話，請將 [訂用帳戶] 從預設值變更為不同的訂用帳戶。

2. 針對 [資源群組]，選取 [新建]，然後輸入新資源群組的名稱並選取 [確認]。

3. 如果您已建立新的資源群組，請選取資源群組的 [區域]。

4. 在 [ **FHIR 服務名稱**] 中，輸入新 Azure API for FHIR 實例的名稱。

5. 選擇您 Azure API for FHIR 的 **位置** 。 此位置可以與資源群組的區域相同或不同。

6. 在 **IoT 連接器名稱** 中提供適用于 FHIR 實例的 Azure IoT Connector 名稱。

7. 針對 [連線 **名稱**] 中的 FHIR，提供在 Azure IoT Connector 內建立的連線名稱。 Azure IoT Central 的應用程式會使用此連線將模擬裝置遙測推送至 Azure IoT Connector 以進行 FHIR。

8. 在 **IoT Central 名稱** 中輸入新的 Azure IoT Central 應用程式的名稱。 此應用程式會使用 *持續性患者監視* 範本來模擬裝置。

9. 從 **IoT Central 位置** 下拉式清單中，選擇 IoT Central 應用程式的位置。 

10. 選取 [檢閱 + 建立]。

11. 閱讀條款及條件，然後選取 [建立]。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

> [!NOTE]
> 如果您想要在本機執行 PowerShell 指令碼，請先輸入 `Connect-AzAccount` 來設定您的 Azure 認證。

如果 `Microsoft.HealthcareApis` 資源提供者尚未針對您的訂用帳戶註冊，您可以使用下列互動式程式碼加以註冊。 若要在 Azure Cloud Shell 中執行程式碼，請在任何程式碼區塊的右上角選取 [試用]。

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.HealthcareApis
```

如果 `Microsoft.IoTCentral` 資源提供者尚未針對您的訂用帳戶註冊，您可以使用下列互動式程式碼加以註冊。 若要在 Azure Cloud Shell 中執行程式碼，請在任何程式碼區塊的右上角選取 [試用]。

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.IoTCentral
```

使用下列程式碼，使用 ARM 範本部署適用于 FHIR 服務的 Azure IoT Connector。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group to contain the service"
$resourceGroupRegion = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$fhirServiceName = Read-Host -Prompt "Enter a name for the new Azure API for FHIR service"
$location = Read-Host -Prompt "Enter an Azure region (for example, westus2) for the service"
$iotConnectorName = Read-Host -Prompt "Enter a name for the new Azure IoT Connector for FHIR"
$connectionName = Read-Host -Prompt "Enter a name for the connection with Azure IoT Connector for FHIR"
$iotCentralName = Read-Host -Prompt "Enter a name for the new Azure IoT Central Application"
$iotCentralLocation = Read-Host -Prompt "Enter a location for the new Azure IoT Central Application"

Write-Verbose "New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion" -Verbose
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion
Write-Verbose "Run New-AzResourceGroupDeployment to create an Azure IoT Connector for FHIR service using an ARM template" -Verbose
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/microsoft/iomt-fhir/master/deploy/templates/managed/azuredeploy.json `
    -fhirServiceName $fhirServiceName `
    -location $location
    -iotConnectorName $iotConnectorName
    -connectionName $connectionName
    -iotCentralName $iotCentralName
    -iotCentralLocation $iotCentralLocation
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

如果 `Microsoft.HealthcareApis` 資源提供者尚未針對您的訂用帳戶註冊，您可以使用下列互動式程式碼加以註冊。 若要在 Azure Cloud Shell 中執行程式碼，請在任何程式碼區塊的右上角選取 [試用]。

```azurecli-interactive
az extension add --name healthcareapis
```

如果 `Microsoft.IoTCentral` 資源提供者尚未針對您的訂用帳戶註冊，您可以使用下列互動式程式碼加以註冊。

```azurecli-interactive
az extension add --name azure-iot
```

使用下列程式碼，使用 ARM 範本部署適用于 FHIR 服務的 Azure IoT Connector。

```azurecli-interactive
read -p "Enter a name for the new resource group to contain the service: " resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group: " resourceGroupRegion &&
read -p "Enter a name for the new Azure API for FHIR service: " fhirServiceName &&
read -p "Enter an Azure region (for example, westus2) for the service: " location &&
read -p "Enter a name for the new Azure IoT Connector for FHIR: " iotConnectorName &&
read -p "Enter a name for the connection with Azure IoT Connector for FHIR: " connectionName &&
read -p "Enter a name for the new Azure IoT Central Application: " iotCentralName &&
read -p "Enter a location for the new Azure IoT Central Application: " iotCentralLocation &&

params='fhirServiceName='$fhirServiceName' location='$location' iotConnectorName='$iotConnectorName' connectionName='$connectionName' iotCentralName='$iotCentralName' iotCentralLocation='$iotCentralLocation &&
echo "CREATE RESOURCE GROUP:  az group create --name $resourceGroupName --location $resourceGroupRegion" &&
az group create --name $resourceGroupName --location $resourceGroupRegion &&
echo "RUN az deployment group create, which creates an Azure IoT Connector for FHIR service using an ARM template" &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/microsoft/iomt-fhir/master/deploy/templates/managed/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

> [!NOTE]
> 部署需要數分鐘的時間才能完成。 請記下 Azure API for FHIR 服務、Azure IoT Central 應用程式和資源群組的名稱，以供稍後用來檢查已部署的資源。

## <a name="review-deployed-resources"></a>檢閱已部署的資源

# <a name="portal"></a>[入口網站](#tab/azure-portal)

請依照下列步驟來查看新 Azure API for FHIR 服務的概觀：

1. 在 [Azure 入口網站](https://portal.azure.com)中，搜尋並選取 [Azure API for FHIR]。

2. 在 FHIR 清單中，選取您的新服務。 新 Azure API for FHIR 服務的 [概觀] 頁面隨即出現。

3. 若要驗證是否已佈建新的 FHIR API 帳戶，請選取 [FHIR 中繼資料端點] 旁的連結，以擷取 FHIR API 功能陳述式。 連結的格式為 `https://<service-name>.azurehealthcareapis.com/metadata`。 如果已佈建帳戶，則會顯示大型 JSON 檔案。

4. 若要驗證是否已布建適用于 FHIR 的新 Azure IoT Connector，請從左側導覽功能表中選取 **IoT Connector (預覽)** ，以開啟 [ **IoT 連接器** ] 頁面。 此頁面必須顯示 FHIR 的已布建 Azure IoT Connector，其 *狀態值* 為 [ *線上*]、[ *連接* 值] 為 *1*，且 [ *裝置對應* ] 和 [ *FHIR 對應* ] 顯示 [ *成功* ] 圖示。

5. 在 [Azure 入口網站](https://portal.azure.com)中，搜尋並選取 [ **IoT Central 應用程式**]。

6. 在 IoT Central 應用程式的清單中，選取您的新服務。 新 IoT Central 應用程式的 [ **總覽** ] 頁面隨即出現。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

執行下列互動式程式碼以檢視 Azure API for FHIR 服務的詳細資料。 您必須輸入新 FHIR 服務和資源群組的名稱。

```azurepowershell-interactive
$fhirServiceName = Read-Host -Prompt "Enter the name of your Azure API for FHIR service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new FHIR service has been provisioned"

$requestUri="https://" + $fhirServiceName + ".azurehealthcareapis.com/metadata"
$metadata = Invoke-WebRequest -Uri $requestUri
$metadata.RawContent
Read-Host "Press [ENTER] to continue"
```

> [!NOTE]
> 適用于 FHIR 的 Azure IoT Connector 目前未提供 PowerShell 命令。 若要驗證 FHIR 的 Azure IoT Connector 是否已正確布建，請使用 [ **入口網站** ] 索引標籤中提供的驗證流程。

執行下列互動式程式碼，以查看您的 Azure IoT Central 應用程式的詳細資料。 您必須輸入新的 IoT Central 應用程式和資源群組的名稱。

```azurepowershell-interactive
$iotCentralName = Read-Host -Prompt "Enter the name of your Azure IoT Central application"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzIotCentralApp -ResourceGroupName $resourceGroupName -Name $iotCentralName" -Verbose
Get-AzIotCentralApp -ResourceGroupName $resourceGroupName -Name $iotCentralName
```

# <a name="cli"></a>[CLI](#tab/CLI)

執行下列互動式程式碼以檢視 Azure API for FHIR 服務的詳細資料。 您必須輸入新 FHIR 服務和資源群組的名稱。

```azurecli-interactive
read -p "Enter the name of your Azure API for FHIR service: " fhirServiceName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az healthcareapis service show --resource-group $resourceGroupName --resource-name $fhirServiceName" &&
az healthcareapis service show --resource-group $resourceGroupName --resource-name $fhirServiceName &&
read -p "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new service has been provisioned: " &&
requestUrl='https://'$fhirServiceName'.azurehealthcareapis.com/metadata' &&
curl --url $requestUrl &&
read -p "Press [ENTER] to continue: "
```

> [!NOTE]
> Azure IoT Connector for FHIR 目前未提供 CLI 命令。 若要驗證 FHIR 的 Azure IoT Connector 是否已正確布建，請使用 [ **入口網站** ] 索引標籤中提供的驗證流程。

執行下列互動式程式碼，以查看您的 Azure IoT Central 應用程式的詳細資料。 您必須輸入新的 IoT Central 應用程式和資源群組的名稱。

```azurecli-interactive
read -p "Enter the name of your IoT Central application: " iotCentralName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az iot central app show -g $resourceGroupName -n $iotCentralName" &&
az iot central app show -g $resourceGroupName -n $iotCentralName &&
```

---

## <a name="connect-your-iot-data-with-the-azure-iot-connector-for-fhir-preview"></a>使用 Azure IoT Connector for FHIR 連線您的 IoT 資料 (預覽)
> [!IMPORTANT]
> 本指南中提供的裝置對應範本是設計用來使用 IoT Central 內的資料匯出 (舊版)。

IoT Central 應用程式目前未提供 ARM 範本或 PowerShell 和 CLI 命令來設定資料匯出。 因此，請遵循下列指示，使用 Azure 入口網站。  

部署 IoT Central 應用程式之後，您的兩個現成的模擬裝置將會開始產生遙測資料。 在此教學課程中，我們會透過 Azure IoT Connector for FHIR，將 *Smart Vitals Patch* 模擬器的遙測資料內嵌到 FHIR 中。 為了將您的 IoT 資料匯出至適用于 FHIR 的 Azure IoT Connector，我們會想要 [在 IoT Central 中設定資料匯出 (舊版) ](../iot-central/core/howto-export-data-legacy.md)。 在 [資料匯出 (舊版) ] 頁面上：
- 挑選 [Azure 事件中樞] 作為匯出目的地。
- 針對 [事件中樞命名空間] 欄位，選取 [使用連接字串] 值。
- 針對 [連接字串] 欄位，提供上一個步驟中取得的 Azure IoT Connector for FHIR 連接字串。
- 在 [要匯出的資料] 欄位上，將 [遙測] 選項維持 [開啟] 狀態。

---

## <a name="view-device-data-in-azure-api-for-fhir"></a>在 Azure API for FHIR 中檢視裝置資料

您可以在 FHIR 伺服器上使用 Postman，查看 Azure IoT Connector 所建立的 FHIR 型觀察資源 (s) FHIR。 設定您的 [Postman 以存取 Azure API for FHIR](access-fhir-postman-tutorial.md)，並向 `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` 提出 `GET` 要求，以檢視含有心率值的觀察 FHIR 資源。

> [!TIP]
> 請確定您的使用者擁有適當的 Azure API for FHIR 資料平面存取權。 使用 [Azure 角色型存取控制 (Azure RBAC)](configure-azure-rbac.md)，指派必要的資料平面角色。

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

在此快速入門指南中，您已經在 Azure API for FHIR 資源中部署 Azure IoT Connector for FHIR 功能。 從下面的後續步驟中選取各項主題，以深入了解 Azure IoT Connector for FHIR：

了解 Azure IoT Connector for FHIR 內不同的資料流程階段。

>[!div class="nextstepaction"]
>[Azure IoT Connector for FHIR 資料流程](iot-data-flow.md)

了解如何使用裝置和 FHIR 對應範本來設定 IoT 連接器。

>[!div class="nextstepaction"]
>[Azure IoT Connector for FHIR 對應範本](iot-mapping-templates.md)

*在 Azure 入口網站中，Azure IoT Connector for FHIR 稱為 IoT 連接器 (預覽)。 FHIR 是 HL7 的註冊商標，可與 HL7 的權限搭配使用。
