---
title: Red Hat Enterprise Linux 自備訂用帳戶的 Azure 映射 |Microsoft Docs
description: 瞭解 Azure 上的 Red Hat Enterprise Linux 自備訂用帳戶映射。
author: asinn826
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 06/10/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 6f2c15518a12a99ca1390284165ad2d48156e06a
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968667"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-gold-images-in-azure"></a>Red Hat Enterprise Linux Azure 中的自備訂用帳戶黃金映射

Red Hat Enterprise Linux (RHEL) 映射可透過隨用隨付或自備訂用帳戶在 Azure 中使用 (BYOS)  (Red Hat 金級映射) 模型。 本文概要說明 Azure 中的 Red Hat 黃金映射。

>[!NOTE]
> RHEL BYOS 黃金映射可在 Azure 公用 (商用) 和 Azure Government 雲端中使用。 它們在 Azure 中國或 Azure Blackforest 雲端中無法使用。

## <a name="important-points-to-consider"></a>需要考量的重要事項

- 此方案中提供的 Red Hat 黃金映射是生產環境就緒的 RHEL 映射，類似于 Azure Marketplace 中的 RHEL 隨用隨付映射。
- 映射會遵循 [Azure 上 Red Hat Enterprise Linux 映射](./redhat-images.md)中所述的目前原則。
- 標準支援原則適用于從這些映射建立的 Vm。
- 從 Red Hat 黃金映射布建的 Vm 不會包含與 RHEL 隨用隨付映射相關聯的 RHEL 費用。
- 影像未獲授權。 您必須使用 Red Hat Subscription-Manager 來註冊並訂閱 Vm，以直接從 Red Hat 取得更新。
- 您可以使用 [Azure Hybrid Benefit](../../linux/azure-hybrid-benefit-linux.md)，從隨用隨付映射切換至 BYOS。 不過，您無法從最初部署的 BYOS 切換至 Linux 映射的隨用隨付計費模型。 若要將計費模式從 BYOS 切換至隨用隨付，您必須從個別的映射重新部署 VM。

>[!NOTE]
> 第2代 RHEL BYOS 映射目前無法透過 marketplace 供應專案取得。 如果您需要第2代 RHEL BYOS 映射，請造訪 Red Hat 訂用帳戶管理中的雲端存取儀表板。 如需詳細資訊，請參閱 [Red Hat 檔](https://access.redhat.com/articles/4847681)。

## <a name="requirements-and-conditions-to-access-the-red-hat-gold-images"></a>存取 Red Hat 黃金映射的需求和條件

1. 熟悉 [Red Hat Cloud Access 方案](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) 條款。 在 [Red Hat 訂](https://access.redhat.com/management/cloud)用帳戶管理員啟用您的 Red hat 訂用帳戶以進行雲端存取。 您必須手動擁有即將註冊進行雲端存取的 Azure 訂用帳戶。

1. 如果您啟用雲端存取的 Red Hat 訂用帳戶符合資格需求，則您的 Azure 訂用帳戶會自動啟用金級影像存取。

### <a name="expected-time-for-image-access"></a>影像存取的預期時間

完成雲端存取啟用步驟之後，Red Hat 會驗證您是否符合 Red Hat 黃金映射的資格。 如果驗證成功，您會在三個小時內收到金級影像的存取權。

## <a name="use-the-red-hat-gold-images-from-the-azure-portal"></a>使用 Azure 入口網站的 Red Hat 黃金映射

1. 當您的 Azure 訂用帳戶收到 Red Hat 黃金影像的存取權之後，您就可以在 [Azure 入口網站](https://portal.azure.com)中找到它們。 移至 [**建立資源] 的**[  >  **查看全部**]。

1. 在頁面頂端，您會看到您有私用供應專案。

    ![Marketplace 私人優惠](./media/rhel-byos-privateoffers.png)

1. 選取紫色連結，或向下滾動至頁面底部，以查看您的私用供應專案。

1. UI 中的其餘部分與任何其他現有的 Red Hat 映射並無不同。 選擇您的 RHEL 版本，並遵循提示來布建您的 VM。 此程式也可讓您在最後一個步驟接受影像的條款。

>[!NOTE]
>到目前為止，這些步驟都不會啟用您的 Red Hat 黃金映射以進行程式設計部署。 需要額外的步驟，如「其他資訊」一節所述。

本檔的其餘部分著重于在映射上布建和接受詞彙的 CLI 方法。 UI 和 CLI 可以完全互換， (已布建的 RHEL 黃金映射 VM) 的最終結果。

## <a name="use-the-red-hat-gold-images-from-the-azure-cli"></a>使用 Azure CLI 的 Red Hat 黃金映射

下列指示將引導您使用 Azure CLI，逐步完成 RHEL VM 的初始部署程式。 這些指示假設您已 [安裝 Azure CLI](/cli/azure/install-azure-cli)。

>[!IMPORTANT]
>請確定所有下列命令的發行者、供應專案、方案和影像參考中都使用所有小寫字母。

1. 確認您是在所需的訂用帳戶中。

    ```azurecli
    az account show -o=json
    ```

1. 為您的 Red Hat 黃金映射 VM 建立資源群組。

    ```azurecli
    az group create --name <name> --location <location>
    ```

1. 接受影像條款。

    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn redhat:rhel-byos:rhel-lvm8:8.0.20190620
    ```

    >[!NOTE]
    >每個 Azure 訂用帳戶 *每個映射 SKU* 都必須接受這些條款一次。

1.  (選擇性) 使用下列命令驗證您的 VM 部署：

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image redhat:rhel-byos:rhel-lvm8:latest --validate
    ```

1. 執行相同的命令（如先前範例所示），而不使用引數來布建您的 VM `--validate` 。

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn>

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image redhat:rhel-byos:rhel-lvm8:latest
    ```

1. 透過 SSH 連線至您的 VM，並確認您有未獲授權映射。 若要執行此步驟，請執行 `sudo yum repolist` 。 針對 RHEL 8，請使用 `sudo dnf repolist` 。 輸出會要求您使用 Subscription-Manager 向 Red Hat 註冊 VM。

>[!NOTE]
>在 RHEL 8 上， `dnf` 和 `yum` 是可互換的。 如需詳細資訊，請參閱 [RHEL 8 系統管理員指南](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/packaging_and_distributing_software/index)。

## <a name="use-the-red-hat-gold-images-from-powershell"></a>從 PowerShell 使用 Red Hat 黃金映射

以下為範例指令碼。 使用您選擇的設定來取代資源群組、位置、VM 名稱、登入資訊和其他變數。 發行者和方案資訊必須是小寫。

```powershell-interactive
    # Variables for common values
    $resourceGroup = "testbyos"
    $location = "canadaeast"
    $vmName = "test01"

    # Define user name and blank password
    $securePassword = ConvertTo-SecureString 'TestPassword1!' -AsPlainText -Force
    $cred = New-Object System.Management.Automation.PSCredential("azureuser",$securePassword)
    Get-AzureRmMarketplaceTerms -Publisher redhat -Product rhel-byos -Name rhel-lvm75 | SetAzureRmMarketplaceTerms -Accept

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

## <a name="encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images"></a>加密 Red Hat Enterprise Linux 自備訂用帳戶黃金映射

Red Hat Enterprise Linux BYOS 黃金映射可透過使用 [Azure 磁碟加密](../../linux/disk-encryption-overview.md)來加以保護。 訂用帳戶 *必須* 先註冊，才能啟用加密。 如需有關如何註冊 RHEL BYOS 黃金圖的詳細資訊，請參閱 [如何使用 Red Hat 訂用帳戶管理員，向 Red Hat 客戶入口網站註冊和訂閱系統](https://access.redhat.com/solutions/253273)。 如果您有作用中的 Red Hat 訂用帳戶，您也可以閱讀 [建立 Red Hat 客戶入口網站啟用金鑰](https://access.redhat.com/articles/1378093)。

[Red Hat 自訂映射](../../linux/redhat-create-upload-vhd.md)不支援 Azure 磁碟加密。 [Linux vm 的 Azure 磁碟加密](../../linux/disk-encryption-overview.md#additional-vm-requirements)中記載了其他 Azure 磁碟加密需求和必要條件。

如需適用 Azure 磁碟加密的步驟，請參閱 [Linux vm 上的 Azure 磁碟加密案例](../../linux/disk-encryption-linux.md) 和相關文章。

## <a name="additional-information"></a>其他資訊

- 如果您嘗試在未針對此供應專案啟用的訂用帳戶上布建 VM，您會收到下列訊息：

    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

    在此情況下，請聯絡 Microsoft 或 Red Hat 來啟用您的訂用帳戶。

- 如果您從 RHEL BYOS 映射修改快照集，並嘗試將該自訂映射發佈至 [共用映射庫](../../linux/shared-image-galleries.md)，您必須提供符合快照集原始來源的方案資訊。 例如，命令看起來可能像這樣：

    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```

    請注意最後一行中的計畫參數。

    自訂映射不支援[Azure 磁碟加密](#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images)。

- 如果您使用自動化從 RHEL BYOS 映射布建 Vm，您必須提供類似于範例命令中所示的方案參數。 例如，如果您使用 Terraform，您可以在 [計畫區塊](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan)中提供方案資訊。

## <a name="next-steps"></a>後續步驟

- Red hat Cloud Access 的詳細資料可在[Red hat 公用雲端檔](https://access.redhat.com/public-cloud)中取得
- 如需有關雲端存取的逐步指南和程式詳細資料，請參閱 [Red Hat Cloud access 檔](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index)。
- 若要深入瞭解 Red Hat 更新基礎結構，請參閱 [Azure Red Hat 更新基礎結構](./redhat-rhui.md)。
- 若要深入瞭解 Azure 中的所有 Red Hat 映射，請參閱 [檔頁面](./redhat-images.md)。
- 如需所有 RHEL 版本的 Red Hat 支援原則的相關資訊，請參閱 [Red Hat Enterprise Linux 生命週期](https://access.redhat.com/support/policy/updates/errata) 頁面。
- 如需 RHEL 黃金映射的其他檔，請參閱 [Red Hat 檔](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure)。
