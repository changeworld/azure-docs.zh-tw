---
title: 建立資源提供者
description: 說明如何建立資源提供者並部署其自訂資源類型。
author: MSEvanhi
ms.topic: tutorial
ms.date: 06/24/2020
ms.author: evanhi
ms.openlocfilehash: 541d140716e52b4fe1db4bc999682914a380a5f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85368102"
---
# <a name="quickstart-create-a-custom-provider-and-deploy-custom-resources"></a>快速入門：建立自訂提供者並部署自訂資源

在本快速入門中，您要建立自己的資源提供者，並部署該資源提供者的自訂資源類型。 如需自訂提供者的詳細資訊，請參閱 [Azure Custom Providers Preview 概觀](overview.md)\(英文\)。

## <a name="prerequisites"></a>必要條件

- 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 若要完成本快速入門中的步驟，您需要呼叫 `REST` 作業。 [REST 要求有各種不同的傳送方式](/rest/api/azure/)\(英文\)。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

- [custom-providers](/cli/azure/ext/custom-providers/custom-providers/resource-provider) 命令需要擴充功能。 如需詳細資訊，請參閱[使用 Azure CLI 擴充功能](/cli/azure/azure-cli-extensions-overview)。
- Azure CLI 範例會針對 `REST` 要求使用 `az rest`。 如需詳細資訊，請參閱 [az rest](/cli/azure/reference-index#az-rest)。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- PowerShell 命令會使用 PowerShell 7 或更新版本以及 Azure PowerShell 模組在本機執行。 如需詳細資訊，請參閱[安裝 Azure PowerShell](/powershell/azure/install-az-ps) (英文)。
- 如果尚未準備好 `REST` 作業適用的工具，請安裝 [ARMClient](https://github.com/projectkudu/ARMClient)。 這是一種開放原始碼命令列工具，可簡化 Azure Resource Manager API 的叫用作業。
- 安裝 **ARMClient** 之後，您可以輸入：`armclient.exe`，從 PowerShell 命令提示字元顯示使用方式資訊。 或者，移至 [ARMClient wiki](https://github.com/projectkudu/ARMClient/wiki)。

---

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-custom-provider"></a>部署自訂提供者

若要設定自訂提供者，請將[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/custom-providers/customprovider.json)部署至您的 Azure 訂用帳戶。

部署範本後，您的訂用帳戶會具有以下資源：

- 函數應用程式，包含資源和動作所適用的作業。
- 儲存體帳戶，用於儲存透過自訂提供者建立的使用者。
- 自訂提供者，定義了自訂資源的類型與動作， 會使用函數應用程式端點來傳送要求。
- 來自於自訂資源提供者的自訂資源。

若要部署自訂提供者，請使用 Azure CLI、owerShell 或 Azure 入口網站：

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

這個範例會提示您輸入資源群組、位置和提供者的函式應用程式名稱。 這些名稱會儲存在其他命令所使用的變數中。 [az group create](/cli/azure/group#az-group-create) 和 [az deployment group create](/cli/azure/deployment/group#az-deployment-group-create) 命令會部署資源。

```azurecli-interactive
read -p "Enter a resource group name:" rgName &&
read -p "Enter the location (i.e. eastus):" location &&
read -p "Enter the provider's function app name:" funcName &&
templateUri="https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json" &&
az group create --name $rgName --location "$location" &&
az deployment group create --resource-group $rgName --template-uri $templateUri --parameters funcName=$funcName &&
echo "Press [ENTER] to continue ..." &&
read
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

這個範例會提示您輸入資源群組、位置和提供者的函式應用程式名稱。 這些名稱會儲存在其他命令所使用的變數中。 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 和 [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) 命令會部署資源。

```powershell
$rgName = Read-Host -Prompt "Enter a resource group name"
$location = Read-Host -Prompt "Enter the location (i.e. eastus)"
$funcName = Read-Host -Prompt "Enter the provider's function app name"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json"
New-AzResourceGroup -Name $rgName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $rgName -TemplateUri $templateUri -funcName $funcName
Read-Host -Prompt "Press [ENTER] to continue ..."
```

---

您也可以從 Azure 入口網站部署解決方案。 選取 [部署至 Azure] 按鈕可在 Azure 入口網站中開啟範本。

[![部署至 Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-docs-json-samples%2Fmaster%2Fcustom-providers%2Fcustomprovider.json)

## <a name="view-custom-provider-and-resource"></a>檢視自訂提供者與資源

在入口網站中，自訂提供者是隱藏的資源類型。 若要確認資源提供者是否已部署，請瀏覽至資源群組。 選取 [顯示隱藏的類型] 選項。

![顯示隱藏的資源類型](./media/create-custom-provider/show-hidden.png)

若要查看您所部署的自訂資源類型，請在您的資源類型上使用 `GET` 作業。

```http
GET https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users?api-version=2018-09-01-preview
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
subID=$(az account show --query id --output tsv)
requestURI="https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users?api-version=2018-09-01-preview"
az rest --method get --uri $requestURI
```

您會收到回應：

```json
{
  "value": [
    {
      "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/santa",
      "name": "santa",
      "properties": {
        "FullName": "Santa Claus",
        "Location": "NorthPole",
        "provisioningState": "Succeeded"
      },
      "resourceGroup": "<rg-name>",
      "type": "Microsoft.CustomProviders/resourceProviders/users"
    }
  ]
}
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$subID = (Get-AzContext).Subscription.Id
$requestURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users?api-version=2018-09-01-preview"

armclient GET $requestURI
```

您會收到回應：

```json
{
  "value": [
    {
      "properties": {
        "provisioningState": "Succeeded",
        "FullName": "Santa Claus",
        "Location": "NorthPole"
      },
      "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/santa",
      "name": "santa",
      "type": "Microsoft.CustomProviders/resourceProviders/users"
    }
  ]
}
```

---

## <a name="call-action"></a>呼叫動作

您的自訂提供者也有名為 `ping` 的動作。 處理該要求的程式碼會在函數應用程式中實作。 `ping` 動作會以問候語回覆。

若要傳送 `ping` 要求，請在您的自訂提供者上使用 `POST` 作業。

```http
POST https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/ping?api-version=2018-09-01-preview
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
pingURI="https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/ping?api-version=2018-09-01-preview"
az rest --method post --uri $pingURI
```

您會收到回應：

```json
{
  "message": "hello <function-name>.azurewebsites.net",
  "pingcontent": {
    "source": "<function-name>.azurewebsites.net"
  }
}
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$pingURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/ping?api-version=2018-09-01-preview"

armclient POST $pingURI
```

您會收到回應：

```json
{
  "pingcontent": {
    "source": "<function-name>.azurewebsites.net"
  },
  "message": "hello <function-name>.azurewebsites.net"
}
```

---

## <a name="create-a-resource-type"></a>建立資源類型

若要建立自訂資源類型，您可以在範本中部署該資源。 在本快速入門過程中部署的範本內，有介紹此種方式。 您也可以傳送資源類型的 `PUT` 要求。

```http
PUT https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/<resource-name>?api-version=2018-09-01-preview

{"properties":{"FullName": "Test User", "Location": "Earth"}}
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
addURI="https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users/testuser?api-version=2018-09-01-preview"
az rest --method put --uri $addURI --body "{'properties':{'FullName': 'Test User', 'Location': 'Earth'}}"
```

您會收到回應：

```json
{
  "id": "/subscriptions/<sub-ID>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/testuser",
  "name": "testuser",
  "properties": {
    "FullName": "Test User",
    "Location": "Earth",
    "provisioningState": "Succeeded"
  },
  "resourceGroup": "<rg-name>",
  "type": "Microsoft.CustomProviders/resourceProviders/users"
}
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$addURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users/testuser?api-version=2018-09-01-preview"
$requestBody = "{'properties':{'FullName': 'Test User', 'Location': 'Earth'}}"

armclient PUT $addURI $requestBody
```

您會收到回應：

```json
{
  "properties": {
    "provisioningState": "Succeeded",
    "FullName": "Test User",
    "Location": "Earth"
  },
  "id": "/subscriptions/<sub-ID>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/testuser",
  "name": "testuser",
  "type": "Microsoft.CustomProviders/resourceProviders/users"
}
```

---

## <a name="custom-resource-provider-commands"></a>自訂資源提供者命令

使用 [custom-providers](/cli/azure/ext/custom-providers/custom-providers/resource-provider) 命令來處理您的自訂資源提供者。

### <a name="list-custom-resource-providers"></a>列出自訂資源提供者

使用 `list` 命令顯示訂用帳戶中的所有自訂資源提供者。 預設會列出目前訂用帳戶的自訂資源提供者，或者您也可以指定 `--subscription` 參數。 若要針對資源群組來列出，請使用 `--resource-group` 參數。

```azurecli-interactive
az custom-providers resource-provider list --subscription $subID
```

```json
[
  {
    "actions": [
      {
        "endpoint": "https://<provider-name>.azurewebsites.net/api/{requestPath}",
        "name": "ping",
        "routingType": "Proxy"
      }
    ],
    "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceproviders/<provider-name>",
    "location": "eastus",
    "name": "<provider-name>",
    "provisioningState": "Succeeded",
    "resourceGroup": "<rg-name>",
    "resourceTypes": [
      {
        "endpoint": "https://<provider-name>.azurewebsites.net/api/{requestPath}",
        "name": "users",
        "routingType": "Proxy, Cache"
      }
    ],
    "tags": {},
    "type": "Microsoft.CustomProviders/resourceproviders",
    "validations": null
  }
]
```

### <a name="show-the-properties"></a>顯示屬性

使用 `show` 命令顯示自訂自訂資源提供者的屬性。 輸出格式類似於 `list` 輸出。

```azurecli-interactive
az custom-providers resource-provider show --resource-group $rgName --name $funcName
```

### <a name="create-a-new-resource"></a>建立新的資源

使用 `create` 命令來建立或更新自訂資源提供者。 這個範例會更新 `actions` 和 `resourceTypes`。

```azurecli-interactive
az custom-providers resource-provider create --resource-group $rgName --name $funcName \
--action name=ping endpoint=https://myTestSite.azurewebsites.net/api/{requestPath} routing_type=Proxy \
--resource-type name=users endpoint=https://myTestSite.azurewebsites.net/api{requestPath} routing_type="Proxy, Cache"
```

```json
"actions": [
  {
    "endpoint": "https://myTestSite.azurewebsites.net/api/{requestPath}",
    "name": "ping",
    "routingType": "Proxy"
  }
],

"resourceTypes": [
  {
    "endpoint": "https://myTestSite.azurewebsites.net/api{requestPath}",
    "name": "users",
    "routingType": "Proxy, Cache"
  }
],
```

### <a name="update-the-providers-tags"></a>更新提供者的標籤

`update` 命令只會更新自訂資源提供者的標籤。 在 Azure 入口網站中，自訂資源提供者的應用程式服務會顯示標籤。

```azurecli-interactive
az custom-providers resource-provider update --resource-group $rgName --name $funcName --tags new=tag
```

```json
"tags": {
  "new": "tag"
},
```

### <a name="delete-a-custom-resource-provider"></a>刪除自訂資源提供者

`delete` 命令會提示您，而且只會刪除自訂資源提供者。 其不會刪除儲存體帳戶、應用程式服務和應用程式服務方案。 提供者遭到刪除後，您就會回到命令提示字元。

```azurecli-interactive
az custom-providers resource-provider delete --resource-group $rgName --name $funcName
```

## <a name="next-steps"></a>後續步驟

如需自訂提供者的簡介，請參閱下列文章：

> [!div class="nextstepaction"]
> [Azure 自訂提供者預覽概觀](overview.md)
