---
title: 使用 Azure Resource Manager 範本 (ARM 範本) 建立 Azure 應用程式組態存放區
titleSuffix: Azure App Configuration
description: 了解如何使用 Azure Resource Manager 範本 (ARM 範本) 建立 Azure 應用程式組態存放區。
author: ZhijunZhao
ms.author: zhijzhao
ms.date: 10/16/2020
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: 56505c95e65911cafbaaa403cd09332695439d97
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/30/2020
ms.locfileid: "97825672"
---
# <a name="quickstart-create-an-azure-app-configuration-store-by-using-an-arm-template"></a>快速入門：使用 ARM 範本建立 Azure 應用程式組態存放區

本快速入門說明如何：

- 使用 Azure Resource Manager 範本 (ARM 範本) 部署應用程式組態存放區。
- 使用 ARM 範本在應用程式組態存放區中建立索引鍵/值。
- 從 ARM 範本讀取應用程式組態存放區中的索引鍵/值。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果您的環境符合必要條件，而且您很熟悉 ARM 範本，請選取 [部署至 Azure] 按鈕。 範本會在 Azure 入口網站中開啟。

[![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store-kv%2Fazuredeploy.json)

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>檢閱範本

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/101-app-configuration-store-kv/)。 其會建立具有兩個索引鍵/值的新應用程式組態存放區。 然後，使用 `reference` 函式來輸出兩個索引鍵/值資源的值。 以這種方式讀取索引鍵的值，可讓該值使用於範本中的其他位置。

本快速入門使用 `copy` 元素來建立索引鍵/值資源的多個執行個體。 若要深入了解 `copy` 元素，請參閱 [ARM 範本中的資源反覆運算](../azure-resource-manager/templates/copy-resources.md)。

> [!IMPORTANT]
> 此範本需要應用程式組態資源提供者版本 `2020-07-01-preview` 或更新版本。 此版本會使用 `reference` 函式來讀取索引鍵/值。 從版本 `2020-07-01-preview` 開始，無法使用先前版本中用來讀取索引鍵/值的 `listKeyValue` 函式。

:::code language="json" source="~/quickstart-templates/101-app-configuration-store-kv/azuredeploy.json":::

範本中定義了兩個 Azure 資源：

- [Microsoft.AppConfiguration/configurationStores](/azure/templates/microsoft.appconfiguration/2020-07-01-preview/configurationstores)：建立應用程式組態存放區。
- [Microsoft.AppConfiguration/configurationStores/keyValues](/azure/templates/microsoft.appconfiguration/2020-07-01-preview/configurationstores/keyvalues)：在應用程式組態存放區內建立索引鍵/值。

> [!TIP]
> `keyValues` 資源的名稱是索引鍵和標籤的組合。 索引鍵和標籤是以 `$` 分隔符號聯結。 標籤是選擇性的。 在上述範例中，名稱為 `myKey` 的 `keyValues` 資源會建立不含標籤的索引鍵/值。
>
> 百分比編碼 (也稱為 URL 編碼) 可讓金鑰或標籤包含 ARM 範本資源名稱中不允許的字元。 `%` 不是允許的字元，因此會在其位置使用 `~`。 若要正確地進行名稱編碼，請遵循下列步驟：
>
> 1. 套用 URL 編碼
> 2. 將 `~` 取代為 `~7E`
> 3. 將 `%` 取代為 `~`
>
> 例如，若要使用索引鍵名稱 `AppName:DbEndpoint` 和標籤名稱 `Test`建立索引鍵/值組，則資源名稱應該為 `AppName~3ADbEndpoint$Test`。

> [!NOTE]
> 應用程式組態可讓您從虛擬網路透過[私人連結](concept-private-endpoint.md)進行索引鍵/值資料存取。 根據預設，此功能啟用時，將會拒絕所有透過公用網路存取應用程式組態資料的要求。 由於 ARM 範本是在您的虛擬網路外執行的，因此不允許從 ARM 範本進行資料存取。 若要在使用私人連結時允許從 ARM 範本進行資料存取，您可以使用下列 Azure CLI 命令來啟用公用網路存取。 在此案例中，請務必考量啟用公用網路存取的安全性影響。
>
> ```azurecli-interactive
> az appconfig update -g MyResourceGroup -n MyAppConfiguration --enable-public-network true
> ```

## <a name="deploy-the-template"></a>部署範本

選取以下影像來登入 Azure 並開啟範本。 範本會建立具有兩個索引鍵/值的應用程式組態存放區。

[![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store-kv%2Fazuredeploy.json)

您也可以使用下列 PowerShell Cmdlet 部署範本。 索引鍵/值會在 PowerShell 主控台的輸出中。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-configuration-store-kv/azuredeploy.json"

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="review-deployed-resources"></a>檢閱已部署的資源

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在 Azure 入口網站搜尋方塊中，鍵入 **應用程式組態**。 從清單中選取 [應用程式組態]。
1. 選取新建立的應用程式組態資源。
1. 在 [作業] 之下，按一下 [組態總管]。
1. 確認有兩個索引鍵/值存在。

## <a name="clean-up-resources"></a>清除資源

若不再需要，可刪除資源群組、應用程式組態存放區和所有相關資源。 如果您打算在未來使用應用程式組態存放區，則可跳過刪除動作。 如果您不要繼續使用此存放區，請執行下列 Cmdlet，以刪除本快速入門所建立的所有資源：

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>後續步驟

若要了解如何使用 Azure 應用程式組態來建立其他應用程式，請繼續閱讀以下文章：

> [!div class="nextstepaction"]
> [快速入門：使用 Azure 應用程式組態建立 ASP.NET Core 應用程式](quickstart-aspnet-core-app.md)
