---
title: 紅帽企業 Linux 自帶訂閱 Azure 映射 |微軟文檔
description: 瞭解在 Azure 上為紅帽企業 Linux 帶來您自己的訂閱映射。
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 825d26307f2b462d51b143b88127e229508f2f25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264592"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-gold-images-in-azure"></a>紅帽企業 Linux 在 Azure 中自帶訂閱的黃金映射

紅帽企業 Linux （RHEL） 映射通過即用即付或自帶訂閱 （BYOS） （紅帽金像）模型在 Azure 中可用。 本文概述了 Azure 中的紅帽金色圖像。

>[!NOTE]
> RHEL BYOS 金像映射在 Azure 公共（商業）和 Azure 政府雲中可用。 它們在 Azure 中國或 Azure 黑林雲中不可用。

## <a name="important-points-to-consider"></a>需要考慮的要點

- 此程式中提供的紅帽金像是生產就緒的 RHEL 映射，類似于 Azure 應用商店中的 RHEL 即用即付映射。
- 這些映射遵循[Azure 上的紅帽企業 Linux 映射中描述的](./redhat-images.md)當前策略。
- 標準支援策略適用于從這些映射創建的 VM。
- 紅帽金像提供的 VM 不攜帶與 RHEL 即用即付圖像相關的 RHEL 費用。
- 圖像是無權的。 您必須使用紅帽訂閱管理器註冊和訂閱 VM，以便直接從紅帽獲取更新。
- 目前無法在 BYOS 和 Linux 映射的即用即付計費模型之間動態切換。 要切換計費模型，必須從相應的映射重新部署 VM。

>[!NOTE]
> 第 2 代 RHEL BYOS 映射目前無法通過市場產品/市場提供。 如果需要第 2 代 RHEL BYOS 映射，請訪問紅帽訂閱管理中的雲訪問儀表板。 有關詳細資訊，請參閱[紅帽文檔](https://access.redhat.com/articles/4847681)。

## <a name="requirements-and-conditions-to-access-the-red-hat-gold-images"></a>訪問紅帽金像的要求和條件

1. 熟悉[紅帽雲訪問計畫](https://www.redhat.com/en/technologies/cloud-computing/cloud-access)條款。 在[紅帽訂閱管理器](https://access.redhat.com/management/cloud)啟用雲訪問紅帽訂閱。 您需要擁有要註冊為雲訪問的 Azure 訂閱。

1. 如果為雲訪問啟用的紅帽訂閱滿足資格要求，則將自動啟用 Azure 訂閱以進行金像訪問。

### <a name="expected-time-for-image-access"></a>映射訪問的預期時間

完成雲訪問啟用步驟後，紅帽將驗證您是否有資格使用紅帽金像。 如果驗證成功，您將在三小時內獲得對黃金圖像的存取權限。

## <a name="use-the-red-hat-gold-images-from-the-azure-portal"></a>使用 Azure 門戶中的紅帽金色圖像

1. Azure 訂閱收到對紅帽金像的存取權限後，可以在[Azure 門戶](https://portal.azure.com)中找到它們。 轉到**創建資源** > **，查看所有**。

1. 在頁面頂部，您將看到您有私人優惠。

    ![市場私人優惠](./media/rhel-byos-privateoffers.png)

1. 選擇紫色連結，或向下滾動到頁面底部以查看您的私人優惠。

1. UI 中的其他預配與任何其他現有的紅帽映射沒有什麼不同。 選擇 RHEL 版本，然後按照提示預配 VM。 此過程還允許您在最後一步接受圖像的條款。

>[!NOTE]
>到目前為止，這些步驟不會啟用您的紅帽金像用於程式設計部署。 如"附加資訊"部分所述，需要執行其他步驟。

本文檔的其餘部分重點介紹用於預配和接受圖像上的術語的 CLI 方法。 就最終結果（預配的 RHEL 金像 VM）而言，UI 和 CLI 是完全可互換的。

## <a name="use-the-red-hat-gold-images-from-the-azure-cli"></a>使用 Azure CLI 中的紅帽金色圖像

以下說明使用 Azure CLI 引導您完成 RHEL VM 的初始部署過程。 這些說明假定已安裝 Azure [CLI。](https://docs.microsoft.com/cli/azure/install-azure-cli)

>[!IMPORTANT]
>請確保在發行者、優惠、計畫和圖像引用中使用以下所有命令中的所有小寫字母。

1. 檢查您是否在所需的訂閱中。

    ```azurecli
    az account show -o=json
    ```

1. 為紅帽金像 VM 創建資源組。

    ```azurecli
    az group create --name <name> --location <location>
    ```

1. 接受圖像術語。

    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn RedHat:rhel-byos:rhel-lvm8:8.0.20190620
    ```

    >[!NOTE]
    >*每個 Azure 訂閱（每個映射 SKU*）需要接受一次這些條款。

1. （可選）使用以下命令驗證 VM 部署：

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image RedHat:rhel-byos:rhel-lvm75:7.5.20190620
    ```

1. 通過運行與上一個示例中顯示的相同命令（沒有參數）`--validate`來預配 VM。

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate
    ```

1. SSH 進入您的 VM，並驗證您擁有無許可權映射。 要執行此步驟，運行`sudo yum repolist`。 對於 RHEL 8，請使用`sudo dnf repolist`。 輸出要求您使用訂閱管理器將 VM 註冊到紅帽。

>[!NOTE]
>在 RHEL `dnf` 8`yum`上，可互換。 有關詳細資訊，請參閱[RHEL 8 管理指南](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/installing-software-with-yum_configuring-basic-system-settings)。

## <a name="use-the-red-hat-gold-images-from-powershell"></a>使用強力殼牌的紅帽金像

以下為範例指令碼。 將資源組、位置、VM 名稱、登錄資訊和其他變數替換為您選擇的配置。 發行者和計畫資訊必須小寫。

```powershell-interactive
    # Variables for common values
    $resourceGroup = "testbyos"
    $location = "canadaeast"
    $vmName = "test01"

    # Define user name and blank password
    $securePassword = ConvertTo-SecureString 'TestPassword1!' -AsPlainText -Force
    $cred = New-Object System.Management.Automation.PSCredential("azureuser",$securePassword)
    Get-AzureRmMarketplaceTerms -Publisher RedHat -Product rhel-byos -Name rhel-lvm75 | SetAzureRmMarketplaceTerms -Accept

    # Create a resource group
    New-AzureRmResourceGroup -Name $resourceGroup -Location $location

    # Create a subnet configuration
    $subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

    # Create a virtual network
    $vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location
    $location `-Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

    # Create a public IP address and specify a DNS name
    $pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location
    $location `-Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

    # Create an inbound network security group rule for port 22
    $nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name
    myNetworkSecurityGroupRuleSSH -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -
    DestinationAddressPrefix * `-DestinationPortRange 22 -Access Allow

    # Create a network security group
    $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location
    $location `-Name myNetworkSecurityGroup -SecurityRules $nsgRuleSSH

    # Create a virtual network card and associate with public IP address and NSG
    $nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -
    Location $location `-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

    # Create a virtual machine configuration
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D3_v2 |
    Set-AzureRmVMOperatingSystem -Linux -ComputerName $vmName -Credential $cred |
    Set-AzureRmVMSourceImage -PublisherName redhat -Offer rhel-byos -Skus rhel-lvm75 -Version latest | Add-     AzureRmVMNetworkInterface -Id $nic.Id
    Set-AzureRmVMPlan -VM $vmConfig -Publisher redhat -Product rhel-byos -Name "rhel-lvm75"

    # Configure SSH Keys
    $sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
    Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

    # Create a virtual machine
    New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

## <a name="encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images"></a>加密紅帽企業 Linux 帶來您自己的訂閱黃金映射

紅帽企業Linux BYOS黃金映射可以通過[使用 Azure 磁片加密](../../linux/disk-encryption-overview.md)進行保護。 *必須先*註冊訂閱，然後才能啟用加密。 有關如何註冊 RHEL BYOS 金像的詳細資訊，請參閱[如何使用紅帽訂閱管理器註冊和訂閱系統到紅帽客戶門戶](https://access.redhat.com/solutions/253273)。 如果您有有效的紅帽訂閱，還可以閱讀[創建紅帽客戶門戶啟用金鑰](https://access.redhat.com/articles/1378093)。

[紅帽自訂映射](../../linux/redhat-create-upload-vhd.md)不支援 Azure 磁片加密。 其他 Azure 磁片加密要求和先決條件記錄[在 Linux VM 的 Azure 磁片加密](../../linux/disk-encryption-overview.md#additional-vm-requirements)中。

有關應用 Azure 磁片加密的步驟，請參閱 Linux VM 和相關文章[上的 Azure 磁片加密方案](../../linux/disk-encryption-linux.md)。

## <a name="additional-information"></a>其他資訊

- 如果嘗試在未為此產品/服務啟用的訂閱上預配 VM，則會收到以下消息：

    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

    在這種情況下，請與 Microsoft 或紅帽聯繫以啟用您的訂閱。

- 如果從 RHEL BYOS 映射修改快照並嘗試將該自訂映射發佈到[共用映射庫](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries)，則必須提供與快照原始源匹配的計畫資訊。 例如，該命令可能如下所示：

    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```

    請注意最終行中的計畫參數。

    自訂映射不支援[Azure 磁片加密](#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images)。

- 如果使用自動化從 RHEL BYOS 映射預配 VM，則必須提供與示例命令中顯示的計畫參數類似。 例如，如果使用 Terraform，則在[計畫塊](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan)中提供計畫資訊。

## <a name="next-steps"></a>後續步驟

- 有關雲訪問的分步指南和程式詳細資訊，請參閱[紅帽雲訪問文檔](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index)。
- 要瞭解有關紅帽更新基礎結構的詳細資訊，請參閱[Azure 紅帽更新基礎結構](./redhat-rhui.md)。
- 要瞭解有關 Azure 中的所有紅帽映射，請參閱[文檔頁](./redhat-images.md)。
- 有關所有版本的 RHEL 的紅帽支援策略的資訊，請參閱[紅帽企業 Linux 生命週期](https://access.redhat.com/support/policy/updates/errata)頁面。
- 有關 RHEL 黃金圖像的其他文檔，請參閱[紅帽文檔](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure)。
