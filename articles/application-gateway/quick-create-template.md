---
title: 快速入門：使用 Resource Manager 範本來引導網路流量
titleSuffix: Azure Application Gateway
description: 了解如何使用 Resource Manager 範本來建立 Azure 應用程式閘道，以將網路流量引導至後端集區中的虛擬機器。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 05/28/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: ebcb51dc4de9283752d61831eb0cad801ec1ac4b
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2020
ms.locfileid: "88705940"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---arm-template"></a>快速入門：使用 Azure 應用程式閘道引導網路流量 - ARM 範本

在此快速入門中，您將使用 Azure Resource Manager 範本 (ARM 範本) 建立 Azure 應用程式閘道。 然後您會測試應用程式閘道，以確保其運作正常。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

您也可以使用 [Azure 入口網站](quick-create-portal.md)、[Azure PowerShell](quick-create-powershell.md) 或 [Azure CLI](quick-create-cli.md) 來完成本快速入門。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

如果您的環境符合必要條件，而且您很熟悉 ARM 範本，請選取 [部署至 Azure] 按鈕。 範本會在 Azure 入口網站中開啟。

[![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-qs%2Fazuredeploy.json)

## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>檢閱範本

為了簡單起見，此範本會建立簡單的設定，包括公用前端 IP、在此應用程式閘道上裝載單一網站的基本接聽程式、基本的要求路由規則，以及後端集區中的兩部虛擬機器。

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/ag-docs-qs/)

:::code language="json" source="~/quickstart-templates/ag-docs-qs/azuredeploy.json":::

範本中定義了多個 Azure 資源：

- [**Microsoft.Network/applicationgateways**](/azure/templates/microsoft.network/applicationgateways)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)：一個用於應用程式閘道，兩個用於虛擬機器。
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)：兩個虛擬機器
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)：兩個用於虛擬機器
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions)：可設定 IIS 和網頁

## <a name="deploy-the-template"></a>部署範本

將 ARM 範本部署到 Azure：

1. 選取 [部署至 Azure] 以登入 Azure 並開啟範本。 此範本會在執行 IIS 的後端集區中建立應用程式閘道、網路基礎結構和兩個虛擬機器。

   [![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-qs%2Fazuredeploy.json)

2. 選取或建立資源群組，輸入虛擬機器系統管理員的使用者名稱和密碼。
3. 選取 [檢閱 + 建立]，然後選取 [建立]。

   部署可能需要 20 分鐘或更久的時間才能完成。

## <a name="validate-the-deployment"></a>驗證部署

雖然不需要 IIS 即可建立應用程式閘道，但仍會加以安裝以確認 Azure 是否已成功建立應用程式閘道。 使用 IIS 測試應用程式閘道：

1. 在 [概觀] 頁面上尋找應用程式閘道的公用 IP 位址。![記錄應用程式閘道公用 IP 位址](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) 或者，您可以選取 [所有資源]，並在搜尋方塊中輸入 *myAGPublicIPAddress*，然後在搜尋結果中加以選取。 Azure 會在 [概觀] 頁面上顯示公用 IP 位址。
2. 將公用 IP 位址複製並貼到您瀏覽器的網址列，以瀏覽該 IP 位址。
3. 檢查回應。 有效的回應會確認應用程式閘道已成功建立，並可與後端順利連線。

   ![測試應用程式閘道](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

   多次重新整理瀏覽器後，您應該會看到 myVM1 和 myVM2 的連線。

## <a name="clean-up-resources"></a>清除資源

當您不再需要先前為應用程式閘道建立的資源時，請刪除資源群組。 這會移除應用程式閘道和所有相關資源。

呼叫 `Remove-AzResourceGroup` Cmdlet 以刪除資源群組：

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 Azure CLI 以應用程式閘道管理網路流量](./tutorial-manage-web-traffic-cli.md)
