---
title: 為 Windows 叢集啟用磁片加密
description: 本文說明如何使用 Azure Resource Manager 中的 Azure Key Vault，為 Azure Service Fabric 叢集節點啟用磁片加密。
ms.topic: article
ms.date: 03/22/2019
ms.openlocfilehash: b08cdb63aa6f334c5a6f7c230b1624d232206c3b
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251809"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-windows"></a>針對 Windows 中的 Azure Service Fabric 叢集節點啟用磁片加密 
> [!div class="op_single_selector"]
> * [適用於 Windows 的磁碟加密](service-fabric-enable-azure-disk-encryption-windows.md)
> * [適用於 Linux 的磁碟加密](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

在本教學課程中，您將瞭解如何在 Windows 中的 Service Fabric 叢集節點上啟用磁片加密。 您必須針對每個節點類型和虛擬機器擴展集執行這些步驟。 為了加密節點，我們將使用虛擬機器擴展集上的 Azure 磁碟加密功能。

本指南涵蓋下列主題：

* 在 Windows 中的 Service Fabric 叢集虛擬機器擴展集上啟用磁片加密時要注意的重要概念。
* 在 Windows 中的 Service Fabric 叢集節點上啟用磁片加密之前，所要遵循的步驟。
* 在 Windows 中的 Service Fabric 叢集節點上啟用磁片加密所要遵循的步驟。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>必要條件

**自我註冊** 

虛擬機器擴展集的磁片加密預覽版需要自行註冊。 使用下列步驟： 

1. 首先，執行下列命令：
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. 等候約10分鐘，直到狀態讀取*已註冊*為止。 您可以執行下列命令來檢查狀態： 
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Azure 金鑰保存庫** 

1. 在與擴展集相同的訂用帳戶和區域中建立金鑰保存庫，然後使用 PowerShell Cmdlet 選取金鑰保存庫上的**EnabledForDiskEncryption**存取原則。 您也可以使用 Azure 入口網站中的 Key Vault UI，並使用下列命令來設定原則：
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. 安裝最新版本的[Azure CLI](/cli/azure/install-azure-cli)，其中包含新的加密命令。
3. 從 Azure PowerShell 版本安裝最新版的[AZURE SDK](https://github.com/Azure/azure-powershell/releases) 。 以下是虛擬機器擴展集 Azure 磁碟加密 Cmdlet 來啟用（[設定](/powershell/module/az.compute/set-azvmssdiskencryptionextension)）加密、取得（[取得](/powershell/module/az.compute/get-azvmssvmdiskencryption)）加密狀態，以及移除（[停](/powershell/module/az.compute/disable-azvmssdiskencryption)用）擴展集實例上的加密。

| 命令 | 版本 |  來源  |
| ------------- |-------------| ------------|
| AzVmssDiskEncryptionStatus   | 1.0.0 版或更新版本 | Az.Compute |
| AzVmssVMDiskEncryptionStatus   | 1.0.0 版或更新版本 | Az.Compute |
| 停用-AzVmssDiskEncryption   | 1.0.0 版或更新版本 | Az.Compute |
| AzVmssDiskEncryption   | 1.0.0 版或更新版本 | Az.Compute |
| AzVmssVMDiskEncryption   | 1.0.0 版或更新版本 | Az.Compute |
| 設定-AzVmssDiskEncryptionExtension   | 1.0.0 版或更新版本 | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>所支援的磁碟加密案例
* 只有使用受控磁片所建立的擴展集，才支援虛擬機器擴展集的加密。 其不支援用於原生 (或非受控) 磁碟擴展集。
* Windows 中虛擬機器擴展集的 OS 和資料磁片區支援加密。 Windows 中虛擬機器擴展集的 OS 和資料磁片區也支援停用加密。
* 目前的預覽中不支援虛擬機器擴展集的虛擬機器重新安裝映射和升級作業。


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>建立新叢集並啟用磁片加密

請使用下列命令來建立叢集，並使用 Azure Resource Manager 範本和自我簽署憑證來啟用磁碟加密。

### <a name="sign-in-to-azure"></a>登入 Azure 
使用下列命令登入：
```powershell
Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```azurecli

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>使用既有的自訂範本 

如果您需要撰寫自訂範本以符合您的需求，我們強烈建議您從[Azure Service Fabric 叢集建立範本範例](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master)頁面上提供的其中一個範本開始。 若要[自訂您][customize-your-cluster-template]的叢集範本區段，請參閱下列指導方針。

如果您已經有自訂範本，請再次檢查範本和參數檔案中的三個與憑證相關的參數，其名稱如下所示，而且這些值都是 null，如下所示：

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

### <a name="deploy-an-application-to-a-service-fabric-cluster-in-windows"></a>將應用程式部署至 Windows 中的 Service Fabric 叢集
若要將應用程式部署到您的叢集，請遵循[使用 PowerShell 部署和移除應用程式](service-fabric-deploy-remove-applications.md)中的步驟和指導方針。


### <a name="enable-disk-encryption-for-the-virtual-machine-scale-sets-created-previously"></a>為先前建立的虛擬機器擴展集啟用磁片加密

若要針對您透過先前步驟所建立的虛擬機器擴展集啟用磁片加密，請執行下列命令：
 
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


### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-windows"></a>驗證 Windows 中的虛擬機器擴展集是否已啟用磁片加密
藉由執行下列命令，取得整個虛擬機器擴展集或擴展集內任何實例的狀態。

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


此外，您可以登入虛擬機器擴展集，並確認磁片磁碟機已加密。

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>針對 Service Fabric 叢集中的虛擬機器擴展集停用磁片加密 
藉由執行下列命令來停用虛擬機器擴展集的磁片加密。 請注意，停用磁片加密會套用至整個虛擬機器擴展集，而不是個別實例。

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>後續步驟
此時，您應該有一個安全的叢集，並知道如何為 Service Fabric 叢集節點和虛擬機器擴展集啟用和停用磁片加密。 如需 Linux 中 Service Fabric 叢集節點的類似指導方針，請參閱[適用于 linux 的磁片加密](service-fabric-enable-azure-disk-encryption-linux.md)。

[customize-your-cluster-template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure#creating-a-custom-arm-template
