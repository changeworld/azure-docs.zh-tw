---
title: 快速入門：使用 Azure Resource Manager 範本 (ARM 範本) 建立 Traffic Manager
description: 本快速入門文章說明如何使用 Azure Resource Manager 範本 (ARM 範本) 來建立 Azure 流量管理員設定檔。
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: duau
ms.date: 09/01/2020
ms.openlocfilehash: dbdb6a255fdf0214103a0011f25b0a6d25014e69
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "89299145"
---
# <a name="quickstart-create-a-traffic-manager-profile-using-an-arm-template"></a>快速入門：使用 ARM 範本建立流量管理員設定檔

本快速入門說明如何使用 Azure Resource Manager 範本 (ARM 範本) 來建立具有外部端點並使用效能路由方法的流量管理員設定檔。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果您的環境符合必要條件，而且您很熟悉 ARM 範本，請選取 [部署至 Azure] 按鈕。 範本會在 Azure 入口網站中開啟。

[![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-traffic-manager-external-endpoint%2Fazuredeploy.json)

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>檢閱範本

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/101-traffic-manager-external-endpoint)。

:::code language="json" source="~/quickstart-templates/101-traffic-manager-external-endpoint/azuredeploy.json":::

此範本中已定義一項 Azure 資源：

* [**Microsoft.Network/trafficManagerProfiles**](/azure/templates/microsoft.network/trafficmanagerprofiles)

若要尋找更多有關 Azure Load Balancer 的範本，請參閱 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)。

## <a name="deploy-the-template"></a>部署範本

1. 選取以下程式碼區塊的 [試用] 以開啟 Azure Cloud Shell，然後遵循指示登入 Azure。 

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-traffic-manager-external-endpoint/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    等候直到您看見主控台的提示字元。

1. 從先前的程式碼區塊選取 [複製] 以複製 PowerShell 指令碼。

1. 以滑鼠右鍵按一下殼層主控台窗格，然後選取 [貼上]。

1. 輸入這些值。

    範本部署會建立具有兩個外部端點的設定檔。 **Endpoint1** 會使用 *w<span>ww.microsoft</span>.com* 的目標端點搭配 [北歐] 的位置。 **Endpoint2** 會使用 *d<span>ocs.microsoft</span>.com* 的目標端點搭配 [美國中南部] 的位置。 

    資源群組名稱是附加 **rg** 的專案名稱。

    > [!NOTE]
    > **uniqueDNSname** 必須是全域唯一的名稱，才能成功部署範本。 如果部署失敗，請從步驟 1 開始。

    部署範本需要幾分鐘的時間。 完成時，輸出如下：

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-arm-powershell-output.png" alt-text="Azure Traffic Manager Resource Manager 範本 PowerShell 部署輸出":::

Azure PowerShell 用於部署範本。 除了 Azure PowerShell 以外，您也可以使用 Azure 入口網站、Azure CLI 和 REST API。 若要了解其他部署方法，請參閱[部署範本](../azure-resource-manager/templates/deploy-portal.md)。

## <a name="validate-the-deployment"></a>驗證部署

1. 使用 [Get-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile)，判斷流量管理員設定檔的 DNS 名稱。

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name ExternalEndpointExample -ResourceGroupName $resourceGroupName | Select RelativeDnsName
    ```

    複製 **RelativeDnsName** 值。 流量管理員設定檔的 DNS 名稱為 *<* relativednsname *>.trafficmanager.net*。 

1. 從本機 PowerShell 執行下列命令，方法是將 **{relativeDNSname}** 變數取代為 *<* relativednsname *>.trafficmanager.net*。

    ```powershell
    Resolve-DnsName -Name {relativeDNSname} | Select-Object NameHost | Select -First 1
    ```
    您應會取得 *w<span>ww.microsoft</span>.com* or *d<span>ocs.microsoft</span>.com* 的 NameHost，這取決於哪個區域較接近您。

1. 若要檢查您是否可解析為其他端點，請針對您在上一個步驟中取得的目標停用端點。 以 **endpoint1** 或 **endpoint2** 取代 **{endpointName}** ，分別停用 *w<span>ww.microsoft</span>.com* 或 *d<span>ocs.microsoft</span>.com*的目標。

    ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name {endpointName} -Type ExternalEndpoints -ProfileName ExternalEndpointExample -ResourceGroupName $resourceGroupName -Force
    ```
1. 在本機 PowerShell 中再次執行步驟 2 中的命令。 此時，您應會取得其他端點的其他 NameHost。 

## <a name="clean-up-resources"></a>清除資源

當您不再需要流量管理員設定檔時，請刪除資源群組。 這會移除流量管理員設定檔和所有相關資源。

呼叫 `Remove-AzResourceGroup` Cmdlet 以刪除資源群組：

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立一個：
* 流量管理員設定檔

若要深入了解如何路由傳送流量，請繼續進行流量管理員的教學課程。

> [!div class="nextstepaction"]
> [流量管理員教學課程](tutorial-traffic-manager-improve-website-response.md)
