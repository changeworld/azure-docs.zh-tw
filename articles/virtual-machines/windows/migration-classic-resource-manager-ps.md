---
title: 使用 PowerShell 移至資源管理員
description: 本文透過使用 Azure PowerShell 命令,介紹支援平臺的 IaaS 資源(如虛擬機器 (VM)、虛擬網路和儲存帳戶從傳統遷移到 Azure 資源管理員的遷移過程
author: tanmaygore
manager: vashan
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 314d7a4725709f00ba5cdbf54595857502bc5805
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81865951"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-powershell"></a>使用 PowerShell 將 IaaS 資源從傳統資源移轉到 Azure 資源管理員

> [!IMPORTANT]
> 今天,大約 90% 的 IaaS VM 正在使用[Azure 資源管理器](https://azure.microsoft.com/features/resource-manager/)。 截至 2020 年 2 月 28 日,經典 VM 已被棄用,將於 2023 年 3 月 1 日完全停用。 [詳細瞭解]( https://aka.ms/classicvmretirement)此棄用[及其如何影響您](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me)。

以下步驟說明如何使用 Azure PowerShell 命令，將基礎結構即服務 (IaaS) 資源從傳統部署模型移轉至 Azure Resource Manager 部署模型。

如果需要,還可以使用[Azure CLI](../linux/migration-classic-resource-manager-cli.md)遷移資源。

* 如需了解有關支援之移轉案例的背景，請參閱 [平台支援的 IaaS 資源移轉 (從傳統移轉至 Azure Resource Manager)](migration-classic-resource-manager-overview.md)。
* 如需詳細的指導方針和移轉逐步解說，請參閱 [平台支援的從傳統移轉至 Azure Resource Manager 的技術深入探討](migration-classic-resource-manager-deep-dive.md)。
* [檢視最常見的移轉錯誤](migration-classic-resource-manager-errors.md)。

<br>
下面是一個流程圖,用於標識在遷移過程中需要執行步驟的順序。

![Screenshot that shows the migration steps](media/migration-classic-resource-manager/migration-flow.png)

 

## <a name="step-1-plan-for-migration"></a>步驟 1︰為移轉做規劃
以下是在評估是否將 IaaS 資源從傳統移到資源管理員時,我們建議的一些最佳實務:

* 將 [支援及不支援的功能和組態](migration-classic-resource-manager-overview.md)看一遍。 如果您有使用不支援的配置或功能的虛擬機器,請等待配置或功能支援被宣佈。 或者，移除該功能或移出該組態以利移轉進行 (如果這麼做符合您的需求)。
* 如果您是使用自動化指令碼來部署現今的基礎結構和應用程式，請使用這些指令碼來嘗試建立相似的測試設定以進行移轉。 或者，您也可以使用 Azure 入口網站來設定範例環境。

> [!IMPORTANT]
> 應用程式閘道目前不受從經典到資源管理員的遷移支援。 要使用應用程式閘道移虛擬網路,請先在運行「準備」操作以行動網路之前刪除閘道。 在完成移轉之後，於 Azure Resource Manager 中重新連接閘道。
>
> 無法自動遷移連接到另一個訂閱中快速路由電路的 Azure ExpressRoute 閘道。 在這種情況下,請刪除 ExpressRoute 閘道、遷移虛擬網路並重新創建閘道。 有關詳細資訊,請參閱遷移[ExpressRoute 電路和相關虛擬網路從經典到資源管理器部署模型](../../expressroute/expressroute-migration-classic-resource-manager.md)。

## <a name="step-2-install-the-latest-version-of-powershell"></a>第二步:安裝最新版本的 PowerShell
Azure PowerShell 的主要安裝選項有兩個：[PowerShell 資源庫](https://www.powershellgallery.com/profiles/azure-sdk/)或 [Web Platform Installer (WebPI)](https://aka.ms/webpi-azps)。 WebPI 接收每月更新。 PowerShell 資源庫則是持續接收更新。 本文是以 Azure PowerShell 2.1.0 為基礎。

如需安裝指示，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azure/overview)。

## <a name="step-3-ensure-that-youre-an-administrator-for-the-subscription"></a>第三步:確保您是訂閱的管理員
要執行此遷移,必須添加為[Azure 門戶](https://portal.azure.com)中的訂閱的共同管理員。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 **"中心"** 選單上,選擇 **"訂閱**"。 如果您沒有看到，請選取 [所有服務]****。
3. 尋找相應的訂閱項目,然後查看 **「MY ROLE」** 欄位。 對於共同管理員,該值應為_帳戶管理員_。

如果您無法添加共同管理員,請與訂閱的服務管理員或共同管理員聯繫以進行添加。

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>第四步:設置訂閱並註冊遷移
首先，開啟 PowerShell 提示字元。 對於遷移,請為經典資源管理員和資源管理員設置環境。

登入您的 Resource Manager 模型帳戶。

```powershell
    Connect-AzAccount
```

請使用下列命令來取得可用的訂用帳戶：

```powershell
    Get-AzSubscription | Sort Name | Select Name
```

設定目前工作階段的 Azure 訂用帳戶。 這個範例會將預設訂用帳戶名稱設定為 [我的 Azure 訂用帳戶]****。 將範例訂用帳戶名稱取代為您自己的名稱。

```powershell
    Select-AzSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> 註冊是一次性步驟,但在嘗試遷移之前必須執行一次。 如果不註冊，您會看到下列錯誤訊息：
>
> *不正確的要求︰訂用帳戶未針對移轉進行註冊。*

請使用下列命令向移轉資源提供者註冊：

```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

等待五分鐘,等待註冊完成。 使用以下指令檢查核准的狀態:

```powershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

請先確定 RegistrationState 是 `Registered` ，再繼續進行。

在切換到經典部署模型之前,請確保在當前部署或虛擬網路的 Azure 區域中有足夠的 Azure 資源管理器虛擬機器 vCPU。 您可以使用下列 PowerShell 命令來檢查您目前在 Azure Resource Manager 中擁有的 vCPU 數目。 若要深入了解 vCPU 配額，請參閱[限制和 Azure Resource Manager](../../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits)。

此範例會檢查**美國西部**區域的可用性。 將範例區域名稱取代為您自己的名稱。

```powershell
    Get-AzVMUsage -Location "West US"
```

現在,登錄到您的帳戶的經典部署模型。

```powershell
    Add-AzureAccount
```

請使用下列命令來取得可用的訂用帳戶：

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

設定目前工作階段的 Azure 訂用帳戶。 這個範例會將預設訂用帳戶設定為 [我的 Azure 訂用帳戶]****。 將範例訂用帳戶名稱取代為您自己的名稱。

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```


## <a name="step-5-run-commands-to-migrate-your-iaas-resources"></a>步驟 5︰執行命令來移轉 IaaS 資源
* [在雲端服務中移至 VM(不在虛擬網路中 )](#step-51-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network)
* [移轉虛擬網路中的 VM](#step-51-option-2---migrate-virtual-machines-in-a-virtual-network)
* [移轉儲存體帳戶](#step-52-migrate-a-storage-account)

> [!NOTE]
> 下述所有作業都是等冪的。 如果您有不支援的功能或組態錯誤以外的任何問題，建議您重新嘗試準備、中止或認可作業。 平台將會重新嘗試該動作。


### <a name="step-51-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>步驟 5.1:選項 1 - 在雲服務中遷移虛擬機(不在虛擬網路中)
使用以下命令獲取雲服務清單。 然後選擇要遷移的雲服務。 如果雲端服務中的 VM 是在虛擬網路中，或是具有 Web 角色或背景工作角色，命令就會傳回錯誤訊息。

```powershell
    Get-AzureService | ft Servicename
```

取得雲端服務的部署名稱。 在此範例中，服務名稱是 **My Service**。 將範例服務名稱取代為您自己的服務名稱。

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

準備好雲端服務中的虛擬機器以進行移轉。 有兩個選項可供您選擇。

* **選項 1:將 VM 遷移到平台創建的虛擬網路。**

    首先,使用以下命令驗證是否可以遷移雲服務:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    下列命令會顯示封鎖移轉的任何警告和錯誤。 如果驗證成功,您可以轉到"準備"步驟。

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **選項 2:遷移到資源管理員部署模型中的現有虛擬網路。**

    此範例將資源群組名稱設定為**myResourceGroup,** 將虛擬網路名稱設為 **「我的虛擬網路**」,並將子網名稱設到**我的 SubNet**。 將此範例中的名稱取代為您自己的資源名稱。

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    首先,驗證是否可以使用以下命令移轉虛擬網路:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    下列命令會顯示封鎖移轉的任何警告和錯誤。 如果驗證成功,您可以繼續執行以下準備步驟:

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

上述其中一個選項的「準備」作業成功之後，請查詢 VM 的移轉狀態。 確保他們處於狀態`Prepared`。

此範例中會將 VM 名稱設定為 **myVM**。 將範例名稱取代為您自己的 VM 名稱。

```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
```

使用 PowerShell 或 Azure 入口網站來檢查已備妥之資源的組態。 如果您尚未準備好進行遷移,並且想要返回舊狀態,請使用以下命令:

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

如果備妥的組態看起來沒問題，您就可以繼續進行並使用下列命令來認可資源：

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="step-51-option-2---migrate-virtual-machines-in-a-virtual-network"></a>步驟 5.1:選項 2 - 在虛擬網路中移轉虛擬機器

若要移轉虛擬網路中的虛擬機器，您將需要移轉虛擬網路。 虛擬機器會自動隨著虛擬網路移轉。 選取您想要移轉的虛擬網路。
> [!NOTE]
> 通過使用虛擬機的 VHD(OS 和資料)檔,使用經典部署模型創建新的資源管理器虛擬機器(具有託管磁碟)遷移使用經典部署模型創建的[單個](migrate-single-classic-to-resource-manager.md)虛擬機器。
<br>

> [!NOTE]
> 虛擬網路名稱可能與新門戶中顯示的名稱不同。 新的 Azure 門戶將名稱`[vnet-name]`顯示為 ,但實際虛擬網路`Group [resource-group-name] [vnet-name]`名稱的類型為 。 在開始遷移之前,請使用 命令查找實際的虛擬網路名稱,或在舊`Get-AzureVnetSite | Select -Property Name`的 Azure 門戶中查看該名稱。 

這個範例會將虛擬網路名稱設定為 **myVnet**。 將範例虛擬網路名稱取代為您自己的名稱。

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> 如果虛擬網路包含 Web 角色或背景工作角色，或有具備不支援之組態的 VM，您就會收到驗證錯誤訊息。

首先,驗證是否可以使用以下命令移轉虛擬網路:

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

下列命令會顯示封鎖移轉的任何警告和錯誤。 如果驗證成功,您可以繼續執行以下準備步驟:

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

請使用 Azure PowerShell 或 Azure 入口網站來檢查已備妥之虛擬機器的組態。 如果您尚未準備好進行遷移,並且想要返回舊狀態,請使用以下命令:

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

如果備妥的組態看起來沒問題，您就可以繼續進行並使用下列命令來認可資源：

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="step-52-migrate-a-storage-account"></a>步驟 5.2:遷移存儲帳戶
遷移虛擬機后,在遷移存儲帳戶之前執行以下先決條件檢查。

> [!NOTE]
> 如果您的儲存帳戶沒有關聯的磁碟或 VM 資料,則可以直接跳到『驗證存儲帳戶並開始遷移』部分。

* 先決條件檢查您是否移轉了任何 VM 或儲存帳戶有磁碟資源:
    * 遷移磁碟存儲在存儲帳戶中的虛擬機器。

        以下命令返回儲存帳戶中所有 VM 磁碟的 RoleName 和 DiskName 屬性。 RoleName 是磁碟所連線的虛擬機器名稱。 如果此命令返回磁碟,請確保在遷移存儲帳戶之前遷移這些磁碟所附加到的虛擬機器。
        ```powershell
         $storageAccountName = 'yourStorageAccountName'
          Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
          DiskName | Format-List -Property RoleName, DiskName

        ```
    * 刪除儲存在儲存帳戶中的未連接 VM 磁碟。

        使用以下指令在儲存帳戶中尋找未連線的 VM 磁碟:

        ```powershell
            $storageAccountName = 'yourStorageAccountName'
            Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

        ```
        如果前面的指令傳回磁碟,請使用以下指令移除這些磁碟:

        ```powershell
           Remove-AzureDisk -DiskName 'yourDiskName'
        ```
    * 刪除存儲在儲存帳戶中的 VM 映射。

        以下命令返回存儲帳戶中儲存 OS 磁碟的所有 VM 映射。
         ```powershell
            Get-AzureVmImage | Where-Object { $_.OSDiskConfiguration.MediaLink -ne $null -and $_.OSDiskConfiguration.MediaLink.Host.Contains($storageAccountName)`
                                    } | Select-Object -Property ImageName, ImageLabel
         ```
         下列命令會傳回將資料磁碟儲存於儲存體帳戶的所有 VM 映像。
         ```powershell

            Get-AzureVmImage | Where-Object {$_.DataDiskConfigurations -ne $null `
                                             -and ($_.DataDiskConfigurations | Where-Object {$_.MediaLink -ne $null -and $_.MediaLink.Host.Contains($storageAccountName)}).Count -gt 0 `
                                            } | Select-Object -Property ImageName, ImageLabel
         ```
        使用以下指令刪除以前指令傳回所有 VM 映像:
        ```powershell
        Remove-AzureVMImage -ImageName 'yourImageName'
        ```
* 驗證存儲帳戶並開始遷移。

    請使用下列命令來驗證每個要移轉的儲存體帳戶。 在此範例中，儲存體帳戶名稱是 **myStorageAccount**。 將範例名稱取代為您自己的儲存體帳戶名稱。

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
    ```

    下一步是準備存儲帳戶進行遷移。

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
    ```

    請使用 Azure PowerShell 或 Azure 入口網站來檢查已備妥之儲存體帳戶的組態。 如果您尚未準備好進行遷移,並且想要返回舊狀態,請使用以下命令:

    ```powershell
        Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
    ```

    如果備妥的組態看起來沒問題，您就可以繼續進行並使用下列命令來認可資源：

    ```powershell
        Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
    ```

## <a name="next-steps"></a>後續步驟
* [平台支援的 IaaS 資源移轉 (從傳統移轉至 Azure Resource Manager) 的概觀](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [平台支援的從傳統移轉至 Azure Resource Manager 的技術深入探討](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [將 IaaS 資源從傳統移轉至 Azure Resource Manager 的規劃](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [使用 CLI 將 IaaS 資源從傳統移轉至 Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [用於協助將 IaaS 資源從傳統移轉至 Azure Resource Manager 的社群工具](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [檢閱最常見的移轉錯誤](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [檢閱有關將 IaaS 資源從傳統移轉至 Azure Resource Manager 的常見問題集](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
