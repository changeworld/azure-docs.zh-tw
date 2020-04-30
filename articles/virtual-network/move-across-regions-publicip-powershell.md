---
title: 使用 Azure PowerShell 將 Azure 公用 IP 移至另一個 Azure 區域
description: 使用 Azure Resource Manager 範本，使用 Azure PowerShell 將 Azure 公用 IP 從一個 Azure 區域移至另一個區域。
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: article
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: 76924705ff801ce3be6a5c76f7ae276bdbf93def
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82147886"
---
# <a name="move-azure-public-ip-to-another-region-using-azure-powershell"></a>使用 Azure PowerShell 將 Azure 公用 IP 移至另一個區域

在許多情況下，您會想要將現有的 Azure 公用 Ip 從一個區域移至另一個區域。 例如，您可能會想要使用相同的設定和 sku 來建立公用 IP 來進行測試。 您也可以將公用 IP 移至另一個區域，做為嚴重損壞修復計畫的一部分。

Azure 公用 Ip 是特定區域，無法從一個區域移至另一個區域。 不過，您可以使用 Azure Resource Manager 範本來匯出公用 IP 的現有設定。  接著，您可以將公用 IP 匯出至範本、修改參數以符合目的地區域，然後將範本部署到新的區域，藉此將資源放在另一個區域中。  如需 Resource Manager 和範本的詳細資訊，請參閱[將資源群組匯出至範本](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>先決條件

- 請確定 Azure 公用 IP 位於您想要移動的 Azure 區域中。

- Azure 公用 Ip 無法在區域之間移動。  您必須將新的公用 ip 與目的地區域中的資源建立關聯。

- 若要匯出公用 IP 設定並部署範本，以在另一個區域中建立公用 IP，您需要網路參與者角色或更高版本。
   
- 識別來源網路配置，以及您目前使用的所有資源。 此配置包括但不限於負載平衡器、網路安全性群組（Nsg）和虛擬網路。

- 確認您的 Azure 訂用帳戶可讓您在使用的目的地區域中建立公用 Ip。 請連絡支援人員啟用所需的配額。

- 請確定您的訂用帳戶有足夠的資源，可支援在此程式中新增公用 Ip。  請參閱 [Azure 訂用帳戶和服務限制、配額與限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)。


## <a name="prepare-and-move"></a>準備和移動
下列步驟示範如何使用 Resource Manager 範本來準備設定移動的公用 IP，並使用 Azure PowerShell 將公用 IP 設定移至目的地區域。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>匯出範本並從腳本部署

1. 使用[disconnect-azaccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)命令登入您的 Azure 訂用帳戶，並遵循畫面上的指示：
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. 取得您想要移至目的地區域之公用 IP 的資源識別碼，並使用[get-azpublicipaddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)將它放在變數中：

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. 將來源虛擬網路匯出至 json 檔案，以在您執行命令[匯出-remove-azresourcegroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)的目錄中：
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. 下載的檔案將會以匯出資源的來源資源群組命名。  找出從名為** \<[資源-群組-名稱>** ] 的命令匯出的檔案，並在您選擇的編輯器中開啟它：
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. 若要編輯公用 IP 名稱的參數，請將來源公用 IP 名稱的屬性**defaultValue**變更為目標公用 ip 的名稱，確定名稱是以引號括住：
    
    ```json
        {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_myVM1pubIP_name": {
        "defaultValue": "<target-publicip-name>",
        "type": "String"
        }
    }

    ```

6. 若要編輯將移動公用 IP 的目的地區域，請變更 [資源] 底下的 [**位置**] 屬性：

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]             
    ```
  
7. 若要取得區域位置代碼，您可以藉由執行下列命令來使用 Azure PowerShell Cmdlet [get-azlocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) ：

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. 您也可以根據您的需求，變更範本中的其他參數，而且是選擇性的：

    * **Sku** -您可以藉由變更** \<資源群組名稱**中的**sku** > **名稱**屬性> json 檔案，將設定中的公用 IP sku 從 standard 變更為「基本」或「基本」到「標準」：

         ```json
            "resources": [
                   {
                    "type": "Microsoft.Network/publicIPAddresses",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                    "location": "<target-region>",
                    "sku": {
                        "name": "Basic",
                        "tier": "Regional"
                    },
         ```

         如需基本和標準 sku 公用 ip 之間差異的詳細資訊，請參閱[建立、變更或刪除公用 IP 位址](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)。

    * **公用 IP 配置方法**和**閒置時間**-您可以在範本中變更這兩個選項，方法是將 [ **PublicIPAllocationMethod** ] 屬性從 [**動態**] 變更為 [**靜態**] 或 [**靜態**] 變更為 [**動態**]。 藉由將**idleTimeoutInMinutes**屬性變更為您想要的數量，可以變更閒置的超時時間。  預設值為**4**：

         ```json
         "resources": [
                {
                "type": "Microsoft.Network/publicIPAddresses",
                "apiVersion": "2019-06-01",
                "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                "location": "<target-region>",
                  "sku": {
                  "name": "Basic",
                  "tier": "Regional"
                 },
                "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
                   }
                }            
         ```

        如需配置方法和閒置超時值的詳細資訊，請參閱[建立、變更或刪除公用 IP 位址](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)。


9. 將** \<資源群組名稱儲存> json**檔案。

10. 在目的地區域中建立資源群組，以使用[remove-azresourcegroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)部署目標公用 IP。
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. 使用[new-azresourcegroupdeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)，將已編輯** \<的資源群組名稱>. json**檔案部署到在上一個步驟中建立的資源群組：

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. 若要確認已在目的地區域中建立資源，請使用[remove-azresourcegroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0)和[get-azpublicipaddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)：
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzPublicIPAddress -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```
## <a name="discard"></a>捨棄 

部署之後，如果您想要在目標中開始或捨棄公用 ip，請刪除在目標中建立的資源群組，將會刪除已移動的公用 IP。  若要移除資源群組，請使用[remove-azresourcegroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)：

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>清除

若要認可變更並完成虛擬網路的移動，請刪除來源虛擬網路或資源群組，使用[remove-azresourcegroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)或[remove-get-azpublicipaddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress?view=azps-2.6.0)：

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzPublicIpAddress -Name <source-publicip-name> -ResourceGroupName <resource-group-name>

```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已將 Azure 公用 IP 從一個區域移至另一個區域，並清除來源資源。  若要深入瞭解如何在 Azure 中的區域和嚴重損壞修復之間移動資源，請參閱：


- [將資源移至新的資源群組或訂用帳戶](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [將 Azure VM 移動到另一個區域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
