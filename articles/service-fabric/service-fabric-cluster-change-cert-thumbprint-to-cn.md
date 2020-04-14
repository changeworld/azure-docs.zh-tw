---
title: 更新叢集以使用憑證的通用名稱
description: 了解如何切換 Service Fabric 叢集，將原本使用憑證指紋的叢集改為使用憑證通用名稱。
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 1926b0501766eb0a5fe086ceada0c9bf45e3dcf6
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272622"
---
# <a name="change-cluster-from-certificate-thumbprint-to-common-name"></a>將叢集從憑證指紋變更為通用名稱
由於憑證的指紋皆不相同，導致叢集憑證變換或管理變成艱難的任務。 然而，不同的憑證卻能擁有相同的通用名稱或主體。  將使用憑證指紋的已部署叢集切換為使用憑證通用名稱，有助於大幅簡化憑證管理作業。 本文章描述如何更新執行中的 Service Fabric 叢集，改為使用憑證通用名稱，而非憑證指紋。

>[!NOTE]
> 如果您的範本中有兩個宣告的指紋，您需要執行兩個部署。  必須先完成第一個部署，才能執行本文中的步驟。  第一個部署會將您範本中的**指紋**屬性設定為要使用的憑證，並移除 **thumbprintSecondary** 屬性。  至於第二個部署，請遵循本文中的步驟。
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-a-certificate"></a>取得憑證
首先，請向[憑證授權單位 (CA)](https://wikipedia.org/wiki/Certificate_authority) 索取憑證。  憑證的一般名稱應該用於您擁有的自訂網域，並且向網域註冊機構購買。 例如，"azureservicefabricbestpractices.com"；並非 Microsoft 員工的人員法佈建 MS 網域的憑證，因此您無法使用您的 LB 或流量管理員本身的 DNS 名稱作為您的憑證一般名稱，而且，如果您的自訂網域可以在 Azure 中解析，您必須佈建 [Azure DNS 區域](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns)。 如果您想要入口網站反映叢集的自訂網域別名，您也需要宣告您擁有的自訂網域成為叢集的 “managementEndpoint”。

基於測試目的，您可以向免費或開放的憑證授權單位索取 CA 簽署憑證。

> [!NOTE]
> 至於自我簽署憑證，包括在 Azure 入口網站中部署 Service Fabric 叢集時產生的憑證則不受支援。 

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>上傳憑證並安裝在擴展集
在 Azure 中，Service Fabric 叢集會部署在虛擬機器擴展集上。  將憑證上傳到金鑰保存庫，然後安裝在叢集執行所在的虛擬機器擴展集上。

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"
$VmssResourceGroupName     = "myclustergroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName `
    -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzKeyVaultCertificate -VaultName $vaultName -Name $certName `
    -FilePath $certFilename -Password $PasswordSec

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
$vmss = Add-AzVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault `
    -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzVmss -ResourceGroupName $VmssResourceGroupName -Verbose `
    -Name $VmssName -VirtualMachineScaleSet $vmss 
```

>[!NOTE]
> 擴展集祕密不支援將相同的資源識別碼用於兩個不同的祕密，因為每個祕密都是已設定版本的唯一資源。 

## <a name="download-and-update-the-template-from-the-portal"></a>從入口網站下載及更新範本
憑證已安裝在基礎擴展集，不過您還需要更新 Service Fabric 叢集，才能使用該憑證和憑證的通用名稱。  現在，請下載叢集部署所需的範本。  登錄到 Azure[門戶](https://portal.azure.com)並導航到託管群集的資源組。  在 [設定]**** 中，選取 [部署]****。  選取最新的部署，然後按一下 [檢視範本]****。

![檢視範本][image1]

將範本和參數 JSON 檔案下載到本機電腦。

首先，請在文字編輯器中開啟參數檔案，然後新增以下參數值：
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

接下來，在文字編輯器中開啟範本檔案，然後更新三個項目以支援憑證通用名稱。

1. 在 **parameters** 區段，新增 *certificateCommonName* 參數：
    ```json
    "certificateCommonName": {
        "type": "string",
        "metadata": {
            "description": "Certificate Commonname"
        }
    },
    ```

    此外,請考慮刪除*證書 Thumbprint,* 它可能不再在資源管理器範本中引用。

2. 在 **Microsoft.Compute/virtualMachineScaleSets** 資源中，更新虛擬機器擴充功能以在憑證設定中使用通用名稱，而非使用指紋。  在**虛擬機器設定檔**->**延伸檔**->**副檔**->**屬性**->**settings**設定->**憑證**中`"commonNames": ["[parameters('certificateCommonName')]"],`,新增`"thumbprint": "[parameters('certificateThumbprint')]",`與刪除 。
    ```json
        "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                            "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                            "nodeTypeRef": "[variables('vmNodeType0Name')]",
                            "dataPath": "D:\\SvcFab",
                            "durabilityLevel": "Bronze",
                            "enableParallelJobs": true,
                            "nicPrefixOverride": "[variables('subnet0Prefix')]",
                            "certificate": {
                                "commonNames": [
                                    "[parameters('certificateCommonName')]"
                                ],
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        "typeHandlerVersion": "1.0"
                    }
                },
    ```

3.  在 **Microsoft.ServiceFabric/clusters** 資源中，將 API 版本更新為 "2018-02-01"。  另外，新增 **certificateCommonNames** 設定並使用 **commonNames** 屬性，同時移除 **certificate** 設定 (使用 thumbprint 屬性)，如以下範例所示：
    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
        ],
        "properties": {
            "addonFeatures": [
                "DnsService",
                "RepairManager"
            ],
            "certificateCommonNames": {
                "commonNames": [
                    {
                        "certificateCommonName": "[parameters('certificateCommonName')]",
                        "certificateIssuerThumbprint": ""
                    }
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
    ```

有關其他資訊[,請參閱部署使用證書通用名稱而不是指紋的服務結構群集。](https://docs.microsoft.com/azure/service-fabric/service-fabric-create-cluster-using-cert-cn)

## <a name="deploy-the-updated-template"></a>部署更新的範本
完成變更之後，重新部署更新的範本。

```powershell
$groupname = "sfclustertutorialgroup"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="next-steps"></a>後續步驟
* 瞭解[叢集安全性](service-fabric-cluster-security.md)。
* 了解如何[變換叢集憑證](service-fabric-cluster-rollover-cert-cn.md)
* [更新及管理叢集憑證](service-fabric-cluster-security-update-certs-azure.md)

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
