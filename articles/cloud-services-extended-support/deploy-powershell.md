---
title: 部署雲端服務 (延伸支援) -PowerShell
description: '使用 PowerShell 部署雲端服務 (延伸支援) '
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 325e9123dd3f121b88df6d03518cfd9300fe3be9
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744968"
---
# <a name="create-a-cloud-service-extended-support-using-azure-powershell"></a>使用 Azure PowerShell 建立雲端服務 (延伸支援) 

本文說明如何使用 `Az.CloudService` PowerShell 模組，在 Azure 中部署具有多個角色的雲端服務 (延伸支援)  (WebRole 和 WorkerRole) 和遠端桌面延伸模組。 

> [!IMPORTANT]
> 雲端服務 (延伸支援) 目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

1. 請參閱雲端服務的 [部署必要條件](deploy-prerequisite.md) (延伸支援) 並建立相關聯的資源。 

3. 安裝 Az. CloudService PowerShell 模組  

    ```powershell
    Install-Module -Name Az.CloudService 
    ```

4. 建立新的資源群組。 如果使用現有的資源群組，則此步驟是選擇性的。   

    ```powershell
    New-AzResourceGroup -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

5. 建立儲存體帳戶和容器，以用來將雲端服務套件儲存 ( .cspkg) 和服務設定 ( .cscfg) 檔。 您必須為儲存體帳戶名稱使用唯一的名稱。 

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName “ContosOrg” -Name “contosostorageaccount” -Location “East US” -SkuName “Standard_RAGRS” -Kind “StorageV2” 
    $container = New-AzStorageContainer -Name “ContosoContainer” -Context $storageAccount.Context -Permission Blob 
    ```

6. 將您的雲端服務套件 (.cspkg) 上傳至儲存體帳戶。

    ```powershell
    $tokenStartTime = Get-Date 
    $tokenEndTime = $tokenStartTime.AddYears(1) 
    $cspkgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cspkg” -Container “ContosoContainer” -Blob “ContosoApp.cspkg” -Context $storageAccount.Context 
    $cspkgToken = New-AzStorageBlobSASToken -Container “ContosoContainer” -Blob $cspkgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cspkgUrl = $cspkgBlob.ICloudBlob.Uri.AbsoluteUri + $cspkgToken 
    ```
 

7.  將您的雲端服務設定 (.cscfg) 上傳至儲存體帳戶。 

    ```powershell
    $cscfgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cscfg” -Container ContosoContainer -Blob “ContosoApp.cscfg” -Context $storageAccount.Context 
    $cscfgToken = New-AzStorageBlobSASToken -Container “ContosoContainer” -Blob $cscfgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cscfgUrl = $cscfgBlob.ICloudBlob.Uri.AbsoluteUri + $cscfgToken 
    ```

8. 建立虛擬網路與子網路。 如果使用現有的網路和子網，則此步驟是選擇性的。 此範例會針對雲端服務角色 (WebRole 和 WorkerRole) 使用單一虛擬網路和子網。 

    ```powershell
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "ContosoWebTier1" -AddressPrefix "10.0.0.0/24" -WarningAction SilentlyContinue 
    $virtualNetwork = New-AzVirtualNetwork -Name “ContosoVNet” -Location “East US” -ResourceGroupName “ContosOrg” -AddressPrefix "10.0.0.0/24" -Subnet $subnet 
    ```
 
9. 建立公用 IP 位址，並 (選擇性地) 設定公用 IP 位址的 DNS 標籤屬性。 如果您使用靜態 IP，則需要在服務設定檔中做為保留的 IP 的參考。  

    ```powershell
    $publicIp = New-AzPublicIpAddress -Name “ContosIp” -ResourceGroupName “ContosOrg” -Location “East US” -AllocationMethod Dynamic -IpAddressVersion IPv4 -DomainNameLabel “contosoappdns” -Sku Basic 
    ```

10. 建立網路設定檔物件，並將公用 IP 位址與平臺建立的負載平衡器前端建立關聯。  

    ```powershell
    $publicIP = Get-AzPublicIpAddress -ResourceGroupName ContosOrg -Name ContosIp  
    $feIpConfig = New-AzCloudServiceLoadBalancerFrontendIPConfigurationObject -Name 'ContosoFe' -PublicIPAddressId $publicIP.Id 
    $loadBalancerConfig = New-AzCloudServiceLoadBalancerConfigurationObject -Name 'ContosoLB' -FrontendIPConfiguration $feIpConfig 
    $networkProfile = @{loadBalancerConfiguration = $loadBalancerConfig} 
    ```
 
11. 建立 Key Vault。 此 Key Vault 將用來儲存與雲端服務相關聯的憑證， (延伸支援) 角色。 Key Vault 必須位於與雲端服務相同的區域和訂用帳戶中，且具有唯一的名稱。 如需詳細資訊，請參閱 [使用憑證搭配 Azure 雲端服務 (延伸支援) ](certificates-and-key-vault.md)。

    ```powershell
    New-AzKeyVault -Name "ContosKeyVault” -ResourceGroupName “ContosoOrg” -Location “East US” 
    ```

13. 更新 Key Vault 存取原則，並將憑證許可權授與您的使用者帳戶。 

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosoOrg' -UserPrincipalName 'user@domain.com' -PermissionsToCertificates create,get,list,delete 
    ```

    或者，透過 ObjectId (設定存取原則，您可以藉由執行 `Get-AzADUser`)  
    
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -ObjectId 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' -PermissionsToCertificates create,get,list,delete 
    ```
 

14. 基於此範例的目的，我們會將自我簽署憑證新增至 Key Vault。 您必須將憑證指紋新增至雲端服務設定 ( .cscfg) 檔案，才能在雲端服務角色上進行部署。 

    ```powershell
    $Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal 
    Add-AzKeyVaultCertificate -VaultName "ContosKeyVault" -Name "ContosCert" -CertificatePolicy $Policy 
    ```
 
15. 建立記憶體內建物件的作業系統設定檔。 作業系統設定檔會指定與雲端服務角色相關聯的憑證。 這會是在上一個步驟中建立的相同憑證。 

    ```powershell
    $keyVault = Get-AzKeyVault -ResourceGroupName ContosOrg -VaultName ContosKeyVault 
    $certificate = Get-AzKeyVaultCertificate -VaultName ContosKeyVault -Name ContosCert 
    $secretGroup = New-AzCloudServiceVaultSecretGroupObject -Id $keyVault.ResourceId -CertificateUrl $certificate.SecretId 
    $osProfile = @{secret = @($secretGroup)} 
    ```

16. 建立角色設定檔的記憶體中物件。 角色設定檔會定義角色 sku 特定的屬性，例如名稱、容量和層級。 在此範例中，我們定義了兩個角色： frontendRole 和 backendRole。 角色設定檔資訊應該符合 configuration (.cscfg 中定義的角色設定) 檔案和服務定義 (配置的) 檔。 

    ```powershell
    $frontendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoFrontend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $backendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoBackend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $roleProfile = @{role = @($frontendRole, $backendRole)} 
    ```

17.  (選擇性) 建立您要新增至雲端服務的擴充設定檔記憶體中物件。 在此範例中，我們將新增 RDP 擴充功能。 

    ```powershell
    $credential = Get-Credential 
    $expiration = (Get-Date).AddYears(1) 
    $extension = New-AzCloudServiceRemoteDesktopExtensionObject -Name 'RDPExtension' -Credential $credential -Expiration $expiration -TypeHandlerVersion '1.2.1' 

    $wadExtension = New-AzCloudServiceDiagnosticsExtension -Name "WADExtension" -ResourceGroupName "ContosOrg" -CloudServiceName "ContosCS" -StorageAccountName "ContosSA" -StorageAccountKey $storageAccountKey[0].Value -DiagnosticsConfigurationPath $configFile -TypeHandlerVersion "1.5" -AutoUpgradeMinorVersion $true 
    $extensionProfile = @{extension = @($rdpExtension, $wadExtension)} 
    ```
18.  (選擇性) 將標記定義為您想要新增至雲端服務的 PowerShell 雜湊表。 

    ```powershell
    $tag=@{"Owner" = "Contoso"} 
    ```

19. 使用設定檔物件 & SAS Url 來建立雲端服務部署。

    ```powershell
    $cloudService = New-AzCloudService                                                  ` 
    -Name “ContosoCS”                                           ` 
    -ResourceGroupName “ContosOrg”                     ` 
    -Location “East US”                                           ` 
    -PackageUrl $cspkgUrl                       ` 
    -ConfigurationUrl $cscfgUrl                                         ` 
    -UpgradeMode 'Auto'                                           ` 
    -RoleProfile $roleProfile                                       ` 
    -NetworkProfile $networkProfile  ` 
    -ExtensionProfile $extensionProfile ` 
    -OSProfile $osProfile  
    -Tag $tag 
    ```

## <a name="next-steps"></a>後續步驟 
- 請 [參閱雲端服務的常見問題，](faq.md) (延伸支援) 。
- 使用 [Azure 入口網站](deploy-portal.md)、 [PowerShell](deploy-powershell.md)、 [範本](deploy-template.md) 或 [Visual Studio](deploy-visual-studio.md)，將雲端服務部署 (延伸支援) 。