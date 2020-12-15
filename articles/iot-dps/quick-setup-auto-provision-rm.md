---
title: 快速入門 - 使用 Azure Resource Manager 範本 (ARM 範本) 建立 Azure IoT 中樞裝置佈建服務 (DPS)
description: Azure 快速入門 - 了解如何使用 Azure Resource Manager 範本 (ARM 範本) 建立 Azure IoT 中樞裝置佈建服務 (DPS)。
author: wesmc7777
ms.author: wesmc
ms.date: 12/03/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc, subject-armqs, devx-track-azurecli
ms.openlocfilehash: 73beed4e4262d911f68c2b4b33bc0c1ee24164f8
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2020
ms.locfileid: "96746196"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-dps-with-an-arm-template"></a>快速入門：使用 ARM 範本設定 IoT 中樞裝置佈建服務 (DPS)

您可以使用 [Azure Resource Manager](../azure-resource-manager/management/overview.md) 範本 (ARM 範本)，以程式設計方式設定佈建裝置所需的 Azure 雲端資源。 這些步驟說明如何使用 ARM 範本建立 IoT 中樞和新的 IoT 中樞裝置佈建服務。 IoT 中樞也會使用範本連結至 DPS 資源。 此連結可讓 DPS 資源根據您設定的配置原則，將裝置指派給中樞。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

本快速入門使用 [Azure 入口網站](../azure-resource-manager/templates/deploy-portal.md)和 [Azure CLI](../azure-resource-manager/templates/deploy-cli.md) 來執行建立資源群組及部署範本所需的程式設計步驟，但您可以輕鬆使用 [PowerShell](../azure-resource-manager/templates/deploy-powershell.md)、.NET、Ruby 或其他程式設計語言來執行這些步驟及部署您的範本。 

如果您的環境符合必要條件，而且已熟悉使用 ARM 範本，則選取下方的 [部署至 Azure] 按鈕將會在 Azure 入口網站中開啟要部署的範本。

[![部署至 Azure 概觀](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-iothub-device-provisioning%2fazuredeploy.json)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]


## <a name="review-the-template"></a>檢閱範本

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/101-iothub-device-provisioning/)。

:::code language="json" source="~/quickstart-templates/101-iothub-device-provisioning/azuredeploy.json":::

上述範本中定義了兩個 Azure 資源：

* [**Microsoft.Devices/iothubs**](/azure/templates/microsoft.devices/iothubs)：建立新的 Azure IoT 中樞。
* [**Microsoft.Devices/provisioningservices**](/azure/templates/microsoft.devices/provisioningservices)：使用已連結的新 IoT 中樞，建立新的 Azure IoT 中樞裝置佈建服務。


## <a name="deploy-the-template"></a>部署範本

#### <a name="deploy-with-the-portal"></a>使用入口網站部署

1. 選取以下映像登入 Azure 並開啟部署範本。 此範本會建立新的 IoT 中樞和 DPS 資源。 該中樞將會連結至 DPS 資源。

    [![在入口網站中部署至 Azure 的步驟](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-iothub-device-provisioning%2fazuredeploy.json)

2. 選取或輸入下列值，然後按一下 [檢閱 + 建立]。

    ![入口網站上的 ARM 範本部署參數](./media/quick-setup-auto-provision-rm/arm-template-deployment-parameters-portal.png)    

    除非下方有指定，否則請使用預設值建立 IoT 中樞和 DPS 資源。

    | 欄位 | 描述 |
    | :---- | :---------- |
    | **訂用帳戶** | 選取 Azure 訂閱。 |
    | **資源群組** | 按一下 [新建] 並輸入資源群組的唯一名稱，然後按一下 [確認]。 |
    | **區域** | 選取資源的區域。 例如，「美國東部」。 |
    | **IoT 中樞名稱** | 輸入在 .azure-devices.net 命名空間中必須是全域唯一的 IoT 中樞名稱。 當您在下一節中驗證部署時，會需要用到此中樞名稱。 |
    | **佈建服務名稱** | 輸入新裝置佈建服務 (DPS) 資源的名稱。 名稱在 .azure-devices-provisioning.net 命名空間中必須是全域唯一的。 當您在下一節中驗證部署時，會需要用到此 DPS 名稱。 |
    
3. 在下一個畫面中，閱讀條款。 如果您同意所有條款，請按一下 [建立]。 

    部署需要數分鐘才能完成。 

    除了 Azure 入口網站以外，您也可以使用 Azure PowerShell、Azure CLI 和 REST API。 若要了解其他部署方法，請參閱[部署範本](../azure-resource-manager/templates/deploy-powershell.md)。


#### <a name="deploy-with-the-azure-cli"></a>使用 Azure CLI 部署

使用 Azure CLI 需要 2.6 版或更新版本。 如果您是在本機執行 Azure CLI，請執行下列參數來驗證您的版本：`az --version`

登入您的 Azure 帳戶並選取您的訂用帳戶。

1. 如果您是在本機執行 Azure CLI，而不是在入口網站中執行，則必須登入。 若要登入命令提示字元中，請執行[登入命令](/cli/azure/get-started-with-az-cli2)：
    
    ```azurecli
    az login
    ```

    依照指示使用程式碼進行驗證，並透過網頁瀏覽器登入 Azure 帳戶。

2. 如果您有多個 Azure 訂用帳戶，則登入 Azure 會授予您所有與認證相關聯之 Azure 帳戶的存取權。 使用下列[命令列出 Azure 帳戶](/cli/azure/account) \(英文\) 以供您使用：
    
    ```azurecli
    az account list -o table
    ```

    使用下列命令，選取您想要用來執行命令以建立 IoT 中樞和 DPS 資源的訂用帳戶。 您可以使用來自上一個命令之輸出內的訂用帳戶名稱或識別碼︰

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

3. 複製下列命令並貼入 CLI 提示字元中。 然後按 **ENTER** 執行命令。
   
    > [!TIP]
    > 命令會提示您輸入資源群組位置。 請先執行下列命令，以便檢視可用位置清單：
    >
    > `az account list-locations -o table`
    >
    >
    
    ```azurecli-interactive
    read -p "Enter a project name that is used for generating resource names:" projectName &&
    read -p "Enter the location (i.e. centralus):" location &&
    templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-iothub-device-provisioning/azuredeploy.json" &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location "$location" &&
    az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
    echo "Press [ENTER] to continue ..." &&
    read
    ```

4. 命令會提示您提供下列資訊。 提供每個值，然後按 **ENTER**。

    | 參數 | 描述 |
    | :-------- | :---------- |
    | **專案名稱** | 此參數的值將用來建立資源群組以保存所有資源。 系統會將字串 `rg` 新增至您資源群組名稱值的結尾處。 |
    | **location** | 此值是所有資源所在的區域。 |
    | **iotHubName** | 輸入在 .azure-devices.net 命名空間中必須是全域唯一的 IoT 中樞名稱。 當您在下一節中驗證部署時，會需要用到此中樞名稱。 |
    | **provisioningServiceName** | 輸入新裝置佈建服務 (DPS) 資源的名稱。 名稱在 .azure-devices-provisioning.net 命名空間中必須是全域唯一的。 當您在下一節中驗證部署時，會需要用到此 DPS 名稱。 |

    AzureCLI 用於部署範本。 除了 Azure CLI，您也可以使用 Azure PowerShell、Azure 入口網站和 REST API。 若要了解其他部署方法，請參閱[部署範本](../azure-resource-manager/templates/deploy-powershell.md)。


## <a name="review-deployed-resources"></a>檢閱已部署的資源

1. 若要確認部署，請執行下列[命令以列出資源](/cli/azure/resource?view=azure-cli-latest#az-resource-list&preserve-view=true)，並在輸出中尋找新的佈建服務和 IoT 中樞：

    ```azurecli
     az resource list -g "${projectName}rg"
    ```

2. 若要確認中樞已連結至 DPS 資源，請執行下列 [DPS extension show 命令](/cli/azure/iot/dps?view=azure-cli-latest#az_iot_dps_show&preserve-view=true)。

    ```azurecli
     az iot dps show --name <Your provisioningServiceName>
    ```

    請注意，中樞會連結至 `iotHubs` 成員。


## <a name="clean-up-resources"></a>清除資源

此集合中的其他快速入門會以本快速入門為基礎。 如果您打算繼續進行後續的快速入門或教學課程，請勿清除在此快速入門中建立的資源。 如果您不打算繼續執行，可以使用 Azure 入口網站或 Azure CLI 刪除資源群組及其所有資源。

若要從 Azure 入口網站刪除資源群組及其資源，只要開啟資源群組，然後按一下頂端的 [刪除資源群組]。

若要使用 Azure CLI 刪除部署的資源群組：

```azurecli
az group delete --name "${projectName}rg"
```

您也可以使用 Azure 入口網站、PowerShell 或 REST API，以及使用針對 Azure Resource Manager 或 IoT 中樞裝置佈建服務發佈的支援平台 SDK，刪除資源群組和個別的資源。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已部署 IoT 中樞和裝置佈建服務執行個體，並已連結這兩個資源。 若要了解如何使用這項設定來佈建裝置，請繼續進行建立裝置的快速入門。

> [!div class="nextstepaction"]
> [佈建裝置的快速入門](./quick-create-simulated-device-symm-key.md)

