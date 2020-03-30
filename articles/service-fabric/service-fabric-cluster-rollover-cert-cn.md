---
title: 滾動 Azure 服務結構群集證書
description: 瞭解如何滾動由證書通用名稱標識的服務結構群集證書。
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 94cc6841886b1b0eb4271ac0f727a2e3561e0081
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451968"
---
# <a name="manually-roll-over-a-service-fabric-cluster-certificate"></a>手動變換 Service Fabric 叢集憑證
當 Service Fabric 叢集憑證即將到期時，您需要更新憑證。  只要叢集已[設定為使用基於通用名稱的憑證](service-fabric-cluster-change-cert-thumbprint-to-cn.md) (而非指紋)，變換憑證將會是一件很簡單的事。  向憑證授權單位索取新到期日的新憑證。  自我簽署的憑證不支援生產 Service Fabric 叢集包含在 Azure 入口網站的叢集建立工作流程期間所產生的憑證。 新憑證的通用名稱必須與舊憑證相同。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

主機上安裝了多個驗證憑證時，Service Fabric 叢集會自動使用具有未來到期日的宣告憑證。 最佳做法是使用 Resource Manager 範本來佈建 Azure 資源。 在非生產環境中，可以使用以下指令碼將新憑證上傳到金鑰保存庫，然後將憑證安裝於虛擬機器擴展集： 

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  <subscription ID>

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "keyvaultgroup"
$VaultName = "cntestvault2"
$certFilename = "C:\users\sfuser\sftutorialcluster20180419110824.pfx"
$certname = "cntestcert"
$Password  = "!P@ssw0rd321"
$VmssResourceGroupName     = "sfclustertutorialgroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Get the key vault.  The key vault must be enabled for deployment.
$keyVault = Get-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName 
$resourceId = $keyVault.ResourceId  

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    

Set-StrictMode -Version 3
$ErrorActionPreference = "Stop"

$certConfig = New-AzVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss = Add-AzVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzVmss -ResourceGroupName $VmssResourceGroupName -Name $VmssName -VirtualMachineScaleSet $vmss  -Verbose
```

>[!NOTE]
> 計算虛擬機器擴展集祕密不支援將相同的資源識別碼用於兩個不同的祕密，因為每個祕密都是已設定版本的唯一資源。 

## <a name="next-steps"></a>後續步驟

* 瞭解[群集安全性](service-fabric-cluster-security.md)。
* [更新及管理叢集憑證](service-fabric-cluster-security-update-certs-azure.md)
