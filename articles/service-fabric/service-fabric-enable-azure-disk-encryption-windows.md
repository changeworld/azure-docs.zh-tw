---
title: 為 Windows 群集啟用磁片加密
description: 本文介紹如何在 Azure 資源管理器中使用 Azure 金鑰保存庫為 Azure 服務結構叢集節點啟用磁片加密。
ms.topic: article
ms.date: 03/22/2019
ms.openlocfilehash: b08cdb63aa6f334c5a6f7c230b1624d232206c3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78251809"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-windows"></a>為 Windows 中的 Azure 服務結構叢集節點啟用磁片加密 
> [!div class="op_single_selector"]
> * [適用於 Windows 的磁碟加密](service-fabric-enable-azure-disk-encryption-windows.md)
> * [適用於 Linux 的磁碟加密](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

在本教程中，您將瞭解如何在 Windows 中的 Service Fabric 叢集節點上啟用磁片加密。 對於每個節點類型和虛擬機器規模集，您需要執行這些步驟。 對於加密節點，我們將在虛擬機器縮放集中使用 Azure 磁片加密功能。

本指南涵蓋以下主題：

* 在 Windows 中啟用 Service Fabric 群集虛擬機器擴展集時需要注意的關鍵概念。
* 在 Windows 中的 Service Fabric 叢集節點上啟用磁片加密之前要執行的步驟。
* 在 Windows 中的 Service Fabric 叢集節點上啟用磁片加密要執行的步驟。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisites

**自我註冊** 

虛擬機器規模集的磁片加密預覽需要自行註冊。 使用下列步驟： 

1. 首先，運行以下命令：
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. 等待大約 10 分鐘，直到狀態為 *"已註冊*"。 您可以通過運行以下命令來檢查狀態： 
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Azure 金鑰保存庫** 

1. 在與規模集相同的訂閱和地區中創建金鑰保存庫，然後使用其 PowerShell Cmdlet 選擇金鑰保存庫上的**啟用ForDisk加密**訪問策略。 還可以使用 Azure 門戶中的金鑰保存庫 UI 使用以下命令來設置策略：
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. 安裝最新版本的 Azure [CLI](/cli/azure/install-azure-cli)，它具有新的加密命令。
3. [從 Azure PowerShell](https://github.com/Azure/azure-powershell/releases)版本安裝最新版本的 Azure SDK。 以下是虛擬機器規模集 Azure 磁片加密 Cmdlet，用於啟用 （[集](/powershell/module/az.compute/set-azvmssdiskencryptionextension)） 加密、 檢索 （[獲取](/powershell/module/az.compute/get-azvmssvmdiskencryption)） 加密狀態， 以及刪除 （[禁用](/powershell/module/az.compute/disable-azvmssdiskencryption)） 加密規模集實例上的加密。

| Command | 版本 |  來源  |
| ------------- |-------------| ------------|
| 獲取 AazVms磁片加密狀態   | 1.0.0 或更高版本 | Az.Compute |
| 獲取-AzVmsVM磁片加密狀態   | 1.0.0 或更高版本 | Az.Compute |
| 禁用-AzVms磁片加密   | 1.0.0 或更高版本 | Az.Compute |
| 獲取-AzVms磁片加密   | 1.0.0 或更高版本 | Az.Compute |
| 獲取-AzVmsVM磁片加密   | 1.0.0 或更高版本 | Az.Compute |
| 設置-AzVms磁片加密擴展   | 1.0.0 或更高版本 | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>所支援的磁碟加密案例
* 虛擬機器規模集的加密僅支援使用託管磁片創建的規模集。 其不支援用於原生 (或非受控) 磁碟擴展集。
* Windows 中的虛擬機器縮放集中的作業系統和資料卷支援加密。 Windows 中虛擬機器規模集的作業系統和資料卷也支援禁用加密。
* 當前預覽版不支援虛擬機器映射和升級虛擬機器規模集的操作。


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>創建新群集並啟用磁片加密

請使用下列命令來建立叢集，並使用 Azure Resource Manager 範本和自我簽署憑證來啟用磁碟加密。

### <a name="sign-in-to-azure"></a>登入 Azure 
使用以下命令登錄：
```powershell
Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```azurecli

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>使用既有的自訂範本 

如果需要創作自訂範本以滿足需求，我們強烈建議您從[Azure 服務結構群集創建範本示例](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master)頁上可用的範本之一開始。 要[自訂群集範本][customize-your-cluster-template]部分，請參閱以下指南。

如果已有自訂範本，請仔細檢查範本和參數檔中的所有三個與證書相關的參數是否命名為如下，並且值是否為 null，如下所示：

```Json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```


```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\templates\templateparam.json"
$templateFilePath="c:\templates\template.json"


New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```


```azurecli

declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath

```

### <a name="deploy-an-application-to-a-service-fabric-cluster-in-windows"></a>將應用程式部署到 Windows 中的服務結構群集
要將應用程式部署到群集，請按照[使用 PowerShell 部署和刪除應用程式](service-fabric-deploy-remove-applications.md)的步驟和指導操作。


### <a name="enable-disk-encryption-for-the-virtual-machine-scale-sets-created-previously"></a>為以前創建的虛擬機器規模集啟用磁片加密

要對通過上述步驟創建的虛擬機器規模集啟用磁片加密，運行以下命令：
 
```powershell

$VmssName = "nt1vm"
$vaultName = "mykeyvault"
$resourceGroupName = "mycluster"
$KeyVault = Get-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName
$DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
$KeyVaultResourceId = $KeyVault.ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType All

```

```azurecli

az vmss encryption enable -g <resourceGroupName> -n <VMSS name> --disk-encryption-keyvault <KeyVaultResourceId>

```


### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-windows"></a>驗證是否為 Windows 中設置的虛擬機器規模啟用了磁片加密
通過運行以下命令獲取整個虛擬機器規模集或規模集中的任何實例的狀態。

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


此外，您可以登錄到虛擬機器規模集，並確保磁碟機已加密。

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>禁用服務交換矩陣群集中設置的虛擬機器規模的磁片加密 
通過運行以下命令，禁用虛擬機器規模集的磁片加密。 請注意，禁用磁片加密適用于整個虛擬機器規模集，而不是單個實例。

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>後續步驟
此時，您應該具有安全群集，並知道如何為 Service Fabric 叢集節點和虛擬機器規模集啟用和禁用磁片加密。 有關 Linux 中的服務交換矩陣叢集節點的類似指南，請參閱[Linux 的磁片加密](service-fabric-enable-azure-disk-encryption-linux.md)。

[customize-your-cluster-template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure#creating-a-custom-arm-template
