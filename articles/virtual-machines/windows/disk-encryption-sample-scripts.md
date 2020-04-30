---
title: Azure 磁碟加密範例指令碼
description: 本文是適用于 Windows Vm 的 Microsoft Azure 磁片加密附錄。
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: e5e0a970df680df43a7bd303636b3d81bda3e141
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82085700"
---
# <a name="azure-disk-encryption-sample-scripts"></a>Azure 磁碟加密範例指令碼 

本文提供準備預先加密的 Vhd 和其他工作的範例腳本。

 

## <a name="list-vms-and-secrets"></a>列出 Vm 和秘密

列出您訂用帳戶中所有已加密的 Vm：

```azurepowershell-interactive
$osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
$dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
```
列出用來加密金鑰保存庫中 Vm 的所有磁片加密秘密：

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
```

## <a name="the-azure-disk-encryption-prerequisites-scripts"></a>Azure 磁碟加密必要條件腳本
如果您已經熟悉 Azure 磁碟加密的必要條件，您可以使用 [Azure 磁碟加密必要條件 PowerShell 指令碼](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 )。 如需使用此 PowerShell 指令碼的範例，請參閱[加密 VM 快速入門](disk-encryption-powershell-quickstart.md)。 您可以從指令碼區段 (起自 211 行) 中移除註解，以對現有資源群組中現有 VM 的所有磁碟加密。 

下表顯示可在 PowerShell 指令碼中使用的參數： 

|參數|描述|是否為強制？|
|------|------|------|
|$resourceGroupName| 金鑰保存庫所屬資源群組的名稱。  如果不存在此名稱的應用程式，將會以此名稱建立新的資源群組。| True|
|$keyVaultName|要用來放置加密金鑰的金鑰保存庫名稱。 如果不存在此名稱的應用程式，將會以此名稱建立新的保存庫。| True|
|$location|金鑰保存庫的位置。 請確定金鑰保存庫和要加密的 VM 位於相同位置。 使用 `Get-AzLocation` 取得位置清單。|True|
|$subscriptionId|要使用的 Azure 訂用帳戶識別碼。  您可以使用 `Get-AzSubscription` 取得您的訂用帳戶識別碼。|True|
|$aadAppName|會用來將祕密寫入到金鑰保存庫的 Azure AD 應用程式名稱。 如果不存在此名稱的應用程式，將會以此名稱建立新的應用程式。 如果此應用程式已經存在，請將 aadClientSecret 參數傳遞至指令碼。|False|
|$aadClientSecret|稍早建立的 Azure AD 應用程式用戶端密碼。|False|
|$keyEncryptionKeyName|金鑰保存庫中選用金鑰加密金鑰的名稱。 如果不存在此名稱的應用程式，將會以此名稱建立新的金鑰。|False|

## <a name="resource-manager-templates"></a>Resource Manager 範本

### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>加密或解密沒有 Azure AD 應用程式的 VM

- [在現有或執行中的 Windows VM 上啟用磁片加密](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)  
- [在執行中的 Windows VM 上停用加密](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad) 

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>加密或解密具有 Azure AD 應用程式 (舊版) 的 VM 
 
- [在現有或執行中的 Windows VM 上啟用磁片加密](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)    
- [在執行中的 Windows VM 上停用加密](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) 
- [從預先加密的 VHD/儲存體 Blob 建立新加密的受控磁碟](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - 建立已加密的新受控磁碟，新的受控磁碟具備預先加密的 VHD 以及對應的加密設定

## <a name="prepare-a-pre-encrypted-windows-vhd"></a>準備已預先加密的 Windows VHD
下列各節是準備預先加密的 Windows VHD 以在 Azure IaaS 中部署為加密的 VHD 的必要項目。 使用該資訊以在 Azure Site Recovery 或 Azure 上準備並啟動全新的 Windows VM (VHD)。 如需有關如何準備和上傳 VHD 的詳細資訊，請參閱[上傳一般化 VHD 並使用它在 Azure 中建立新的 VM](upload-generalized-managed.md)。

### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>更新群組原則以對作業系統保護允許非 TPM
設定 **BitLocker 磁碟機加密**的 BitLocker 群組原則設定，其位於此路徑**本機電腦原則** > **電腦設定** > **系統管理範本** > **Windows 元件**。 將此設定變更為**作業系統磁片磁碟機** > ，**啟動** > 時需要額外的驗證，**允許不含相容 TPM 的 BitLocker**，如下圖所示：

![Azure 中的 Microsoft Antimalware](../media/disk-encryption/disk-encryption-fig8.png)

### <a name="install-bitlocker-feature-components"></a>安裝 BitLocker 功能元件
針對 Windows Server 2012 和更新版本，請使用下列命令︰

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

針對 Windows Server 2008 R2，請使用下列命令︰

    ServerManagerCmd -install BitLockers

### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>使用 `bdehdcfg` 準備用於 BitLocker 的 OS 磁碟區
若要壓縮作業系統磁碟分割並為 BitLocker 準備電腦，請視需要執行 [bdehdcfg](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-basic-deployment)：

    bdehdcfg -target c: shrink -quiet 

### <a name="protect-the-os-volume-by-using-bitlocker"></a>使用 BitLocker 保護作業系統磁碟區
使用[`manage-bde`](https://technet.microsoft.com/library/ff829849.aspx)命令，以使用外部金鑰保護裝置在開機磁碟區上啟用加密。 也將外部金鑰 (.bek 檔案) 放置於外部磁碟機或磁碟區。 下次重新開機後，會在系統/開機磁碟區上啟用加密。

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> 使用不同的資料/資源 VHD 來準備 VM，才能使用 BitLocker 取得外部金鑰。

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>上傳加密的 VHD 至 Azure 儲存體帳戶
啟用 DM Crypt 加密之後，需要將本機加密的 VHD 上傳至您的儲存體帳戶。
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```

## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>將已預先加密 VM 的祕密上傳至金鑰保存庫
您先前取得的磁片加密密碼必須上傳，做為金鑰保存庫中的秘密。  這需要將 [設定秘密] 許可權和 [wrapkey] 許可權授與將上傳秘密的帳戶。

```powershell 
# Typically, account Id is the user principal name (in user@domain.com format)
$upn = (Get-AzureRmContext).Account.Id
Set-AzKeyVaultAccessPolicy -VaultName $kvname -UserPrincipalName $acctid -PermissionsToKeys wrapKey -PermissionsToSecrets set

# In cloud shell, the account ID is a managed service identity, so specify the username directly 
# $upn = "user@domain.com" 
# Set-AzKeyVaultAccessPolicy -VaultName $kvname -UserPrincipalName $acctid -PermissionsToKeys wrapKey -PermissionsToSecrets set

# When running as a service principal, retrieve the service principal ID from the account ID, and set access policy to that 
# $acctid = (Get-AzureRmContext).Account.Id
# $spoid = (Get-AzureRmADServicePrincipal -ServicePrincipalName $acctid).Id
# Set-AzKeyVaultAccessPolicy -VaultName $kvname -ObjectId $spoid -BypassObjectIdValidation -PermissionsToKeys wrapKey -PermissionsToSecrets set

```

### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>未使用 KEK 加密的磁碟加密密碼
若要在金鑰保存庫中設定密碼，請使用[AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret)。 複雜密碼會編碼為 base64 字串，然後上傳至金鑰保存庫。 此外，請確定在金鑰保存庫中建立密碼時會設定下列標籤。

```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


在下一個步驟中使用 `$secretUrl`，以便[在不使用 KEK 的狀況下連接 OS 磁碟](#without-using-a-kek)。

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>使用 KEK 加密的磁碟加密密碼
將密碼上傳至金鑰保存庫之前，您可以選擇性地使用金鑰加密金鑰來加密密碼。 使用包裝 [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) 先加密使用金鑰加密金鑰的密碼。 此 wrap 作業的輸出是 base64 URL 編碼的字串，您可以使用[`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret) Cmdlet 上傳為秘密。

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id
```

在下一個步驟中使用 `$KeyEncryptionKey` 和 `$secretUrl`，以便[使用 KEK 連接 OS 磁碟](#using-a-kek)。

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>連接 OS 磁碟時，指定密碼的 URL

###  <a name="without-using-a-kek"></a>不使用 KEK
當您在連接 OS 磁碟時，需要傳遞 `$secretUrl`。 URL 已在「未使用 KEK 加密的磁碟加密密碼」一節中產生。
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Windows `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl
```
### <a name="using-a-kek"></a>使用 KEK
連接 OS 磁碟時，傳遞 `$KeyEncryptionKey` 和 `$secretUrl`。 URL 已在「使用 KEK 加密的磁碟加密祕密」一節中產生。
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Windows `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id
```
