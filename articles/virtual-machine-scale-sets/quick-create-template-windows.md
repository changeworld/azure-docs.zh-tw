---
title: 快速入門 - 使用 Azure 範本建立 Windows 虛擬機器擴展集
description: 了解如何使用 Azure Resource Manager 範本快速建立 Windows 虛擬機器擴展，可部署範例應用程式及設定自動縮放規則
author: ju-shim
ms.author: jushiman
ms.topic: quickstart
ms.service: virtual-machine-scale-sets
ms.subservice: windows
ms.date: 03/27/2020
ms.reviewer: mimckitt
ms.custom: mimckitt, subject-armqs
ms.openlocfilehash: a4cd39186eb55cb03bd97994baa1898e196fb3c2
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86082806"
---
# <a name="quickstart-create-a-windows-virtual-machine-scale-set-with-an-arm-template"></a>快速入門：使用 ARM 範本建立 Windows 虛擬機器擴展集

虛擬機器擴展集可讓您部署和管理一組自動調整的虛擬機器。 您可以手動調整擴展集中的 VM 數目，或定義規則以根據 CPU、記憶體需求或網路流量等資源使用量進行自動調整。 其後，Azure 負載平衡器會將流量分配到擴展集中的多個 VM 執行個體。 在此快速入門中，您會使用 Azure Resource Manager 範本 (ARM 範本) 建立虛擬機器擴展集，並部署範例應用程式。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

ARM 範本可讓您部署相關資源的群組。 在單一範本中，您可以建立虛擬機器擴展集、安裝應用程式，並設定自動縮放規則。 使用變數和參數，就可以重複使用此範本來更新現有的、或建立其他的擴展集。 您可以透過 Azure 入口網站、Azure CLI、Azure PowerShell，或從持續整合/持續傳遞 (CI/CD) 管線部署範本。

如果您的環境符合必要條件，而且您很熟悉 ARM 範本，請選取 [部署至 Azure] 按鈕。 範本會在 Azure 入口網站中開啟。

[![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-windows-webapp-dsc-autoscale%2Fazuredeploy.json)

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>檢閱範本

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/201-vmss-windows-webapp-dsc-autoscale/)。

:::code language="json" source="~/quickstart-templates/201-vmss-windows-webapp-dsc-autoscale/azuredeploy.json" range="1-397" highlight="236-325":::

這些範本中定義了下列資源：

- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadbalancers)
- [**Microsoft.Compute/virtualMachineScaleSets**](/azure/templates/microsoft.compute/virtualmachinescalesets)
- [**Microsoft.Insights/autoscaleSettings**](/azure/templates/microsoft.insights/autoscalesettings)

### <a name="define-a-scale-set"></a>定義擴展集

反白顯示的部分是擴展集資源定義。 若要使用範本建立縮放，您可以定義適當的資源。 虛擬機器擴展集資源類型的核心部分是：

| 屬性                     | 屬性描述                                  | 範例範本值                    |
|------------------------------|----------------------------------------------------------|-------------------------------------------|
| type                         | 要建立的 Azure 資源類型                            | Microsoft.Compute/virtualMachineScaleSets |
| NAME                         | 擴展集名稱                                       | myScaleSet                                |
| location                     | 要建立擴展集的位置                     | 美國東部                                   |
| sku.name                     | 每個擴展集執行個體的 VM 大小                  | Standard_A1                               |
| sku.capacity                 | 最初要建立的 VM 執行個體數目           | 2                                         |
| upgradePolicy.mode           | 發生變更時的 VM 執行個體升級模式              | 自動                                 |
| imageReference               | 要用於 VM 執行個體的平台或自訂映像 | Microsoft Windows Server 2016 資料中心  |
| osProfile.computerNamePrefix | 每個 VM 執行個體的名稱前置詞                     | myvmss                                    |
| osProfile.adminUsername      | 每個 VM 執行個體的使用者名稱                        | azureuser                                 |
| osProfile.adminPassword      | 每個 VM 執行個體的密碼                        | P@ssw0rd!                                 |

若要自訂擴展集範本，您可以變更 VM 大小或初始容量， 或使用不同的平台或自訂映像。

### <a name="add-a-sample-application"></a>新增範例應用程式

若要測試您的擴展集，請安裝基本的 Web 應用程式。 當您部署擴展集時，VM 延伸模組可以提供後置部署設定和自動化工作，例如安裝應用程式。 您可以從 Azure 儲存體或 GitHub 下載指令碼，或是在擴充功能執行階段將指令碼提供給 Azure 入口網站。 若要將延伸模組套用至擴展集，請將 extensionProfile 區段新增至上述資源範例。 延伸模組設定檔通常會定義下列屬性：

- 延伸模組類型
- 延伸模組發行者
- 延伸模組版本
- 設定或安裝指令碼的位置
- 在 VM 執行個體上執行的命令

範本會使用 PowerShell DSC 延伸模組來安裝 IIS 中執行的 ASP.NET MVC 應用程式。

會從 GitHub 下載安裝指令碼，如 url 中所定義。 然後，延伸模組會從 IISInstall.ps1 指令碼執行 InstallIIS，如函式和指令碼中所定義。 ASP.NET 應用程式本身會依 Web Deploy 套件提供，也是從 GitHub 下載，如 WebDeployPackagePath 中所定義：

## <a name="deploy-the-template"></a>部署範本

您可以選取 [部署至 Azure] 按鈕來部署範本。 這個按鈕會開啟 Azure 入口網站、載入完整的範本，並提示輸入幾個參數，例如擴展集名稱、執行個體計數和管理員認證。

[![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-windows-webapp-dsc-autoscale%2Fazuredeploy.json)

您也可以使用 Azure PowerShell 部署 Resource Manager 範本：

```azurepowershell-interactive
# Create a resource group
New-AzResourceGroup -Name myResourceGroup -Location EastUS

# Deploy template into resource group
New-AzResourceGroupDeployment `
    -ResourceGroupName myResourceGroup `
    -TemplateURI https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/azuredeploy.json

# Update the scale set and apply the extension
Update-AzVmss `
    -ResourceGroupName myResourceGroup `
    -VmScaleSetName myVMSS `
    -VirtualMachineScaleSet $vmssConfig
```

回答提示以提供擴展集名稱和 VM 執行個體的管理員認證。 可能需要 10-15 分鐘才能建立擴展集，並套用延伸模組來設定應用程式。

## <a name="validate-the-deployment"></a>驗證部署

若要查看有效的擴展集，請在網頁瀏覽器中存取範例 Web 應用程式。 使用 [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) 取得負載平衡器的公用 IP 位址，如下所示：

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

以 http:\//publicIpAddress/MyApp 格式，將負載平衡器的公用 IP 位址輸入網頁瀏覽器中。 負載平衡器會將流量散發至您的其中一個 VM 執行個體，如下列範例所示：

![執行中的 IIS 網站](./media/virtual-machine-scale-sets-create-powershell/running-iis-site.png)

## <a name="clean-up-resources"></a>清除資源

若不再需要，您可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 移除資源群組和擴展集。 `-Force` 參數會確認您想要刪除資源，而不另外對您提示將要進行此作業。 `-AsJob` 參數不會等待作業完成，就會將控制項傳回給提示字元。

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```

## <a name="next-steps"></a>後續步驟

在此快速入門中，您使用 ARM 範本建立了 Windows 擴展集，並使用 PowerShell DSC 延伸模組，在 VM 執行個體上安裝基本的 ASP.NET 應用程式。 若要深入了解，請繼續執行有關於如何建立和管理 Azure 虛擬機器擴展集的教學課程。

> [!div class="nextstepaction"]
> [建立和管理 Azure 虛擬機器擴展集](tutorial-create-and-manage-powershell.md)
