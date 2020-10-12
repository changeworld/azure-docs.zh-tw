---
title: 使用憑證通用名稱建立叢集
description: 了解如何使用憑證通用名稱，並依據範本建立 Service Fabric 叢集。
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: c852b40d35f936753d3c16420159676da239b6c6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86246430"
---
# <a name="deploy-a-service-fabric-cluster-that-uses-certificate-common-name-instead-of-thumbprint"></a>部署使用憑證通用名稱 (而非指紋) 的 Service Fabric 叢集
由於憑證的指紋皆不相同，導致叢集憑證變換或管理變成艱難的任務。 然而，不同的憑證卻能擁有相同的通用名稱或主體。  使用憑證通用名稱的叢集能大幅簡化憑證管理工作的難度。 本文章描述如何部署 Service Fabric 叢集才能使用憑證通用名稱，而非憑證指紋。
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-a-certificate"></a>取得憑證
首先，請向[憑證授權單位 (CA)](https://wikipedia.org/wiki/Certificate_authority) 索取憑證。  憑證的一般名稱應該用於您擁有的自訂網域，並且向網域註冊機構購買。 例如，"azureservicefabricbestpractices.com"；並非 Microsoft 員工的人員法佈建 MS 網域的憑證，因此您無法使用您的 LB 或流量管理員本身的 DNS 名稱作為您的憑證一般名稱，而且，如果您的自訂網域可以在 Azure 中解析，您必須佈建 [Azure DNS 區域](../dns/dns-delegate-domain-azure-dns.md)。 如果您想要入口網站反映叢集的自訂網域別名，您也需要宣告您擁有的自訂網域成為叢集的 “managementEndpoint”。

基於測試目的，您可以向免費或開放的憑證授權單位索取 CA 簽署憑證。

> [!NOTE]
> 至於自我簽署憑證，包括在 Azure 入口網站中部署 Service Fabric 叢集時產生的憑證則不受支援。 

## <a name="upload-the-certificate-to-a-key-vault"></a>將憑證上傳到金鑰保存庫
在 Azure 中，Service Fabric 叢集會部署在虛擬機器擴展集上。  將憑證上傳到金鑰保存庫。  部署叢集時，憑證會安裝在叢集執行所在的虛擬機器擴展集上。

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

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

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
```

## <a name="download-and-update-a-sample-template"></a>下載及更新範例範本
本文章使用 [5 節點安全叢集範例](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure)範本和範本參數。 將 *azuredeploy.json* 和 *azuredeploy.parameters.json* 檔案下載到電腦。

### <a name="update-parameters-file"></a>更新參數檔案
首先，請在文字編輯器中開啟 *azuredeploy.parameters.json* 檔案，然後新增以下參數值：
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"certificateIssuerThumbprint": {
    "value": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
},
```

接著，將 *certificateCommonName*、*sourceVaultValue* 及 *certificateUrlValue* 參數值設定為前述指令碼傳回的值：
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"certificateIssuerThumbprint": {
    "value": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
},
"sourceVaultValue": {
  "value": "/subscriptions/<subscription>/resourceGroups/testvaultgroup/providers/Microsoft.KeyVault/vaults/testvault"
},
"certificateUrlValue": {
  "value": "https://testvault.vault.azure.net:443/secrets/testcert/5c882b7192224447bbaecd5a46962655"
},
```

### <a name="update-the-template-file"></a>更新範本檔案
接下來，在文字編輯器中開啟 *azuredeploy.json* 檔案，然後更新三個項目以支援憑證通用名稱。

1. 在 **parameters** 區段，新增 *certificateCommonName* 參數：
    ```json
    "certificateCommonName": {
      "type": "string",
      "metadata": {
        "description": "Certificate Commonname"
      }
    },
    "certificateIssuerThumbprint": {
      "type": "string",
      "metadata": {
        "description": "Certificate Authority Issuer Thumpbrint for Commonname cert"
      }
    },
    ```

    另外，請考慮移除 *certificateThumbprint*，因為我們不再需要它了。

2. 將 *sfrpApiVersion* 變數的值設定為 "2018-02-01"：
    ```json
    "sfrpApiVersion": "2018-02-01",
    ```

3. 在 **Microsoft.Compute/virtualMachineScaleSets** 資源中，更新虛擬機器擴充功能以在憑證設定中使用通用名稱，而非使用指紋。  在**virtualMachineProfile** -> **extensionProfile** -> **extensions** -> **properties** -> **settings** -> **certificate**中，add 
    ```json
       "commonNames": [
        "[parameters('certificateCommonName')]"
       ],
    ```

    並移除 `"thumbprint": "[parameters('certificateThumbprint')]",`。

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

4. 在 **Microsoft.ServiceFabric/clusters** 資源中，將 API 版本更新為 "2018-02-01"。  另外，新增 **certificateCommonNames** 設定並使用 **commonNames** 屬性，同時移除 **certificate** 設定 (使用 thumbprint 屬性)，如以下範例所示：
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
               "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
           }
           ],
           "x509StoreName": "[parameters('certificateStoreValue')]"
       },
       ...
   ```
   > [!NOTE]
   > [certificateIssuerThumbprint] 欄位允許搭配指定的主體一般名稱來指定預期的憑證簽發者。 此欄位會接受以逗號分隔的 SHA1 指紋列舉。 請注意，這是對憑證驗證的強化；在未指定簽發者或簽發者為空白的情況下，若憑證的鏈結可以建置，且會落在受驗證者信任的根目錄下，系統將會接受該憑證。 若指定簽發者，在憑證直接簽發者的指紋符合此欄位中所指定之任何值的情況下 (無論根目錄是否被信任)，系統將會接受該憑證。 請注意，PKI 可能會使用不同的憑證授權單位來為相同的主體簽發憑證，因此請務必針對指定主體指定所有預期的簽發者指紋。
   >
   > 指定簽發者是最佳的做法；雖然省略它仍然可以運作 (針對鏈結至受信任根目錄的憑證)，此行為仍具有限制，並可能會在不久的將來被移除。 此外，請注意到在 Azure 中部署、受到由私人 PKI 簽發的 X509 憑證所保護，並由主體所宣告的叢集，在該 PKI 的憑證原則不可探索、不可用且不可存取的情況下，該叢集可能會無法由 Azure Service Fabric 服務進行驗證 (針對叢集對服務通訊)。 

## <a name="deploy-the-updated-template"></a>部署更新的範本
完成變更之後，重新部署更新的範本。

```powershell
# Variables.
$groupname = "testclustergroup"
$clusterloc="southcentralus"  
$id="<subscription ID"

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $id 

# Create a new resource group and deploy the cluster.
New-AzResourceGroup -Name $groupname -Location $clusterloc

New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\AzureDeploy.Parameters.json" -TemplateFile "C:\temp\cluster\AzureDeploy.json" -Verbose
```

## <a name="next-steps"></a>後續步驟
* 瞭解叢集 [安全性](service-fabric-cluster-security.md)。
* 了解如何[變換叢集憑證](service-fabric-cluster-rollover-cert-cn.md)
* [更新及管理叢集憑證](service-fabric-cluster-security-update-certs-azure.md)
* 藉由[將叢集從憑證指紋變更為一般名稱](service-fabric-cluster-change-cert-thumbprint-to-cn.md)來簡化憑證管理

[image1]: .\media\service-fabric-cluster-change-cert-thumbprint-to-cn\PortalViewTemplates.png
