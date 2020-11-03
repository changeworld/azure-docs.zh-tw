---
title: 建立 Azure DNS 區域和記錄 - Azure Resource Manager 範本 (ARM 範本)
titleSuffix: Azure DNS
description: 了解如何在 Azure DNS 中建立 DNS 區域和記錄。 這份逐步快速入門將使用 Azure Resource Manager 範本 (ARM 範本) 建立和管理第一個 DNS 區域和記錄。
services: dns
author: duongau
ms.service: dns
ms.topic: quickstart
ms.date: 09/8/2020
ms.author: duau
ms.custom: subject-armqs
ms.openlocfilehash: 24460167e2279e7d3001d0bc16d050beb5b55289
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790996"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-an-arm-template"></a>使用 ARM 範本建立 Azure DNS 區域和記錄

本快速入門說明如何使用 Azure Resource Manager 範本 (ARM 範本) 建立具備 `A` 記錄的 DNS 區域。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果您的環境符合必要條件，而且您很熟悉 ARM 範本，請選取 [部署至 Azure] 按鈕。 範本會在 Azure 入口網站中開啟。

[![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-dns-new-zone%2Fazuredeploy.json)

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>檢閱範本

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/101-azure-dns-new-zone)。

在本快速入門中，您將建立唯一的 DNS 區域，其尾碼為 `azurequickstart.org`。 指向兩個 IP 位址的 `A` 記錄也會放在此區域中。

:::code language="json" source="~/quickstart-templates/101-azure-dns-new-zone/azuredeploy.json":::

範本中已定義兩個 Azure 資源：

- [**Microsoft.Network/dnsZones**](/azure/templates/microsoft.network/dnsZones)
- [**Microsoft.Network/dnsZones/A**](/azure/templates/microsoft.network/dnsZones/A)：用來在區域中建立 `A` 記錄。

若要尋找更多有關 Azure Load Balancer 的範本，請參閱 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)。

## <a name="deploy-the-template"></a>部署範本

1. 選取以下程式碼區塊的 [試用] 以開啟 Azure Cloud Shell，然後遵循指示登入 Azure。

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-dns-new-zone/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    等候直到您看見主控台的提示字元。

1. 從先前的程式碼區塊選取 [複製] 以複製 PowerShell 指令碼。

1. 以滑鼠右鍵按一下殼層主控台窗格，然後選取 [貼上]。

1. 輸入這些值。

    範本部署會建立一個區域，其中有一個指向兩個 IP 位址的 `A` 記錄。 資源群組名稱是附加 **rg** 的專案名稱。

    需要幾秒鐘來部署範本。 完成時，輸出如下：

    :::image type="content" source="./media/dns-getstarted-template/create-dns-zone-powershell-output.png" alt-text="Azure DNS 區域 Resource Manager 範本 PowerShell 部署輸出":::

Azure PowerShell 用於部署範本。 除了 Azure PowerShell 以外，您也可以使用 Azure 入口網站、Azure CLI 和 REST API。 若要了解其他部署方法，請參閱[部署範本](../azure-resource-manager/templates/deploy-portal.md)。

## <a name="validate-the-deployment"></a>驗證部署

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 選取左側面板中的 [資源群組]。

1. 選取您在上一節中建立的資源群組。 預設的資源群組名稱是附加 **rg** 的專案名稱。

1. 資源群組應包含下列資源：

    :::image type="content" source="./media/dns-getstarted-template/resource-group-dns-zone.png" alt-text="DNS 區域部署資源群組":::

1. 選取尾碼為 `azurequickstart.org` 的 DNS 區域，以確認已使用 `A` 記錄 (參考 `1.2.3.4` 和 `1.2.3.5` 的值) 正確建立區域。

    :::image type="content" source="./media/dns-getstarted-template/dns-zone-overview.png" alt-text="DNS 區域部署":::

1. 從上一個步驟複製其中一個名稱伺服器名稱。

1. 開啟命令提示字元，並執行下列命令：

   ```cmd
   nslookup www.<dns zone name> <name server name>
   ```

   例如：

   ```cmd
   nslookup www.2lwynbseszpam.azurequickstart.org ns1-09.azure-dns.com.
   ```

   您應該會看到類似於下列螢幕擷取畫面的內容：

    :::image type="content" source="./media/dns-getstarted-template/dns-zone-validation.png" alt-text="DNS 區域 nslookup":::

主機名稱 `www.2lwynbseszpam.azurequickstart.org` 解析為 `1.2.3.4` 和 `1.2.3.5`，正如您所設定。 此結果確認了名稱解析正常運作。

## <a name="clean-up-resources"></a>清除資源

當您不再需要先前為 DNS 區域建立的資源時，請刪除資源群組。 這會移除 DNS 區域和所有相關資源。

呼叫 `Remove-AzResourceGroup` Cmdlet 以刪除資源群組：

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立一個：

- DNS 區域
- `A` 記錄

既然您已經使用 ARM 範本建立第一個 DNS 區域和第一筆記錄，就可以在自訂網域中為 Web 應用程式建立記錄。

> [!div class="nextstepaction"]
> [在自訂網域中建立 Web 應用程式的 DNS 記錄](./dns-web-sites-custom-domain.md)
