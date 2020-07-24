---
title: Red Hat Enterprise Linux 攜帶您自己的訂用帳戶 Azure 映射 |Microsoft Docs
description: 瞭解 Azure 上 Red Hat Enterprise Linux 的自備訂用帳戶映射。
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
ms.date: 06/10/2020
ms.author: alsin
ms.openlocfilehash: 54d703b8a493610174f00844cd0736f65f3ee541
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87052167"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-gold-images-in-azure"></a>Red Hat Enterprise Linux 在 Azure 中攜帶自己的訂用帳戶黃金映射

Red Hat Enterprise Linux （RHEL）映射可透過隨用隨付或自備訂用帳戶（BYOS）（Red Hat 黃金圖）模型，在 Azure 中提供。 本文概要說明 Azure 中的 Red Hat 黃金映射。

>[!NOTE]
> RHEL BYOS 黃金映射適用于 Azure 公用（商業）和 Azure Government 雲端。 它們無法在 Azure 中國或 Azure Blackforest 雲端中使用。

## <a name="important-points-to-consider"></a>需要考慮的要點

- 此方案中提供的 Red Hat 黃金映射是已準備好用於生產環境的 RHEL 映射，類似于 Azure Marketplace 中的 RHEL 隨用隨付映射。
- 映射會遵循[Azure 上 Red Hat Enterprise Linux 映射](./redhat-images.md)中所述的目前原則。
- 標準支援原則適用于從這些映射建立的 Vm。
- 從 Red Hat 黃金映射布建的 Vm 不會包含與 RHEL 隨用隨付映射相關聯的 RHEL 費用。
- 映射是未獲授權的。 您必須使用 Red Hat 訂用帳戶管理員來註冊並訂閱 Vm，以直接從 Red Hat 取得更新。
- 目前無法在適用于 Linux 映射的 BYOS 與隨用隨付計費模型之間動態切換。 若要切換計費模型，您必須從個別映射重新部署 VM。

>[!NOTE]
> 第2代 RHEL BYOS 映射目前無法透過 marketplace 供應專案使用。 如果您需要第2代 RHEL BYOS 映射，請造訪 Red Hat 訂用帳戶管理中的雲端存取儀表板。 如需詳細資訊，請參閱[Red Hat 檔](https://access.redhat.com/articles/4847681)。

## <a name="requirements-and-conditions-to-access-the-red-hat-gold-images"></a>存取 Red Hat 黃金影像的需求和條件

1. 熟悉[Red Hat 雲端存取計畫](https://www.redhat.com/en/technologies/cloud-computing/cloud-access)條款。 在[Red Hat 訂](https://access.redhat.com/management/cloud)用帳戶管理員中，啟用您的 red hat 訂閱以進行雲端存取。 您必須擁有即將註冊以進行雲端存取的 Azure 訂用帳戶。

1. 如果您為雲端存取啟用的 Red Hat 訂用帳戶符合資格需求，則會自動啟用您的 Azure 訂用帳戶以獲得黃金影像存取。

### <a name="expected-time-for-image-access"></a>影像存取的預期時間

完成雲端存取啟用步驟之後，Red Hat 會驗證您是否符合 Red Hat 黃金映射的資格。 如果驗證成功，您可以在三個小時內獲得黃金影像的存取權。

## <a name="use-the-red-hat-gold-images-from-the-azure-portal"></a>使用來自 Azure 入口網站的 Red Hat 黃金映射

1. 在您的 Azure 訂用帳戶收到 Red Hat 黃金映射的存取權之後，您就可以在[Azure 入口網站](https://portal.azure.com)中找到它們。 移至 [**建立資源**] [  >  **查看全部**]。

1. 在頁面頂端，您會看到您有私用供應專案。

    ![Marketplace 私人優惠](./media/rhel-byos-privateoffers.png)

1. 選取紫色連結，或向下流覽至頁面底部，以查看您的私用供應專案。

1. 在 UI 中的其餘部分與其他現有的 Red Hat 映射並無不同。 選擇您的 RHEL 版本，並遵循提示來布建您的 VM。 此程式也可讓您在最後一個步驟中接受映射的條款。

>[!NOTE]
>到目前為止，這些步驟不會啟用您的 Red Hat 黃金映射來進行程式設計部署。 需要額外的步驟，如「其他資訊」一節中所述。

本檔的其餘部分著重于在映射上布建和接受條款的 CLI 方法。 與最終結果（已布建的 RHEL 黃金映射 VM）相關，UI 和 CLI 可完全互換。

## <a name="use-the-red-hat-gold-images-from-the-azure-cli"></a>使用來自 Azure CLI 的 Red Hat 黃金映射

下列指示會逐步引導您使用 Azure CLI 來完成 RHEL VM 的初始部署程式。 這些指示假設您已[安裝 Azure CLI](/cli/azure/install-azure-cli)。

>[!IMPORTANT]
>請務必在所有下列命令的發行者、供應專案、計畫和映射參考中使用所有小寫字母。

1. 檢查您是否在所要的訂用帳戶中。

    ```azurecli
    az account show -o=json
    ```

1. 為您的 Red Hat 黃金映射 VM 建立資源群組。

    ```azurecli
    az group create --name <name> --location <location>
    ```

1. 接受影像詞彙。

    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn redhat:rhel-byos:rhel-lvm8:8.0.20190620
    ```

    >[!NOTE]
    >針對每個*Azure 訂用帳戶，每個映射 SKU 必須接受一次*這些條款。

1. 選擇性使用下列命令來驗證您的 VM 部署：

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image redhat:rhel-byos:rhel-lvm8:latest --validate
    ```

1. 執行與上一個範例中所示相同的命令，以布建您的 VM，而不使用 `--validate` 引數。

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn>

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image redhat:rhel-byos:rhel-lvm8:latest
    ```

1. 透過 SSH 連線到您的 VM，並確認您有未獲授權映射。 若要執行此步驟，請執行 `sudo yum repolist` 。 若為 RHEL 8，請使用 `sudo dnf repolist` 。 輸出會要求您使用訂用帳戶管理員向 Red Hat 註冊 VM。

>[!NOTE]
>在 RHEL 8 上， `dnf` 和 `yum` 可互換。 如需詳細資訊，請參閱[RHEL 8 系統管理指南](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/packaging_and_distributing_software/index)。

## <a name="use-the-red-hat-gold-images-from-powershell"></a>使用 PowerShell 中的 Red Hat 黃金映射

以下為範例指令碼。 使用您選擇的設定來取代資源群組、位置、VM 名稱、登入資訊和其他變數。 發行者和計畫資訊必須是小寫。

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

## <a name="encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images"></a>加密 Red Hat Enterprise Linux 自備訂用帳戶金級映射

Red Hat Enterprise Linux BYOS 黃金映射可以透過使用[Azure 磁碟加密](../../linux/disk-encryption-overview.md)來保護。 您*必須*先註冊訂用帳戶，才能啟用加密。 如需如何註冊 RHEL BYOS 黃金映射的詳細資訊，請參閱[如何使用 Red Hat 訂用帳戶管理員向 Red Hat 客戶入口網站註冊並訂閱系統](https://access.redhat.com/solutions/253273)。 如果您有作用中的 Red Hat 訂用帳戶，您也可以閱讀[建立 Red Hat 客戶入口網站啟用金鑰](https://access.redhat.com/articles/1378093)。

[Red Hat 自訂映射](../../linux/redhat-create-upload-vhd.md)不支援 Azure 磁碟加密。 其他 Azure 磁碟加密需求和必要條件記載于[Linux vm 的 Azure 磁碟加密](../../linux/disk-encryption-overview.md#additional-vm-requirements)中。

如需適用 Azure 磁碟加密的步驟，請參閱[Linux vm 上的 Azure 磁碟加密案例](../../linux/disk-encryption-linux.md)和相關文章。

## <a name="additional-information"></a>其他資訊

- 如果您嘗試在未啟用此供應專案的訂用帳戶上布建 VM，您會收到下列訊息：

    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

    在此情況下，請聯絡 Microsoft 或 Red Hat 以啟用您的訂用帳戶。

- 如果您從 RHEL BYOS 映射修改快照，並嘗試將該自訂映射發佈到[共用映射資源庫](../../linux/shared-image-galleries.md)，則必須提供符合快照集原始來源的計畫資訊。 例如，命令看起來可能像這樣：

    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```

    請注意最後一行中的計畫參數。

    自訂映射不支援[Azure 磁碟加密](#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images)。

- 如果您使用自動化從 RHEL BYOS 映射布建 Vm，您必須提供類似于範例命令中所示的計畫參數。 例如，如果您使用 Terraform，則會在[計畫區塊](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan)中提供計畫資訊。

## <a name="next-steps"></a>後續步驟

- 如需 Red Hat 雲端存取的更多詳細資料，請[參閱 Red hat 公用雲端檔](https://access.redhat.com/public-cloud)\ （英文 \）
- 如需雲端存取的逐步指南和程式詳細資料，請參閱[Red Hat 雲端存取檔](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index)。
- 若要深入瞭解 Red Hat 更新基礎結構，請參閱[Azure Red Hat 更新基礎結構](./redhat-rhui.md)。
- 若要深入瞭解 Azure 中的所有 Red Hat 映射，請參閱[檔頁面](./redhat-images.md)。
- 如需所有 RHEL 版本的 Red Hat 支援原則資訊，請參閱[Red Hat Enterprise Linux 生命週期](https://access.redhat.com/support/policy/updates/errata)頁面。
- 如需 RHEL 黃金影像的其他檔，請參閱[Red Hat 檔](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure)。
