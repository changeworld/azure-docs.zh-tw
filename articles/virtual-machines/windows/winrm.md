---
title: 設定 Azure VM 的 WinRM 存取
description: 設定 WinRM 存取給 Resource Manager 部署模型中建立的 Azure 虛擬機器使用。
author: mimckitt
manager: vashan
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/16/2016
ms.author: mimckitt
ms.openlocfilehash: fbba14980083287c91fdd3defec78b229808fb15
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621683"
---
# <a name="setting-up-winrm-access-for-virtual-machines-in-azure-resource-manager"></a>在 Azure Resource Manager 中設定虛擬機器的 WinRM 存取

以下是您對 VM 設定 WinRM 連線必須採取的步驟

1. 建立金鑰保存庫
2. 建立自我簽署憑證
3. 將您的自我簽署憑證上傳至金鑰保存庫
4. 取得金鑰保存庫中您的自我簽署憑證的 URL
5. 在建立 VM 時參考您的自我簽署憑證的 URL

 

## <a name="step-1-create-a-key-vault"></a>步驟 1︰建立金鑰保存庫
您可以使用下列命令來建立金鑰保存庫

```azurepowershell
New-AzKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## <a name="step-2-create-a-self-signed-certificate"></a>步驟 2：建立自我簽署憑證
您可以使用下列 PowerShell 指令碼建立自我簽署憑證

```azurepowershell
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## <a name="step-3-upload-your-self-signed-certificate-to-the-key-vault"></a>步驟 3：將自我簽署憑證上傳至金鑰保存庫
在將憑證上傳至於步驟 1 中建立的金鑰保存庫之前，必須先將它轉換成 Microsoft.Compute 資源提供者可以了解的格式。 下面的 PowerShell 指令碼可讓您這麼做

```azurepowershell
$fileName = "<Path to the .pfx file>"
$fileContentBytes = Get-Content $fileName -Encoding Byte
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

[System.Collections.HashTable]$TableForJSON = @{
    "data"     = $filecontentencoded;
    "dataType" = "pfx";
    "password" = "<password>";
}
[System.String]$JSONObject = $TableForJSON | ConvertTo-Json

$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
Set-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>" -SecretValue $secret
```

## <a name="step-4-get-the-url-for-your-self-signed-certificate-in-the-key-vault"></a>步驟 4：取得金鑰保存庫中您的自我簽署憑證的 URL
Microsoft.Compute 資源提供者在佈建 VM 時，需要金鑰保存庫內密碼的 URL。 這可讓 Microsoft.Compute 資源提供者下載密碼，並在 VM 上建立對等憑證。

> [!NOTE]
> 密碼的 URL 也必須包含版本。 範例 URL 如下所示： HTTPs： \/ /contosovault.vault.azure.net:443/secrets/contososecret/01h9db0df2cd4300a20ence585a6s7ve

#### <a name="templates"></a>範本
您可以使用下列程式碼取得範本中的 URL 連結

```json
"certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"
```

#### <a name="powershell"></a>PowerShell
您可以使用下列 PowerShell 命令取得此 URL 

```azurepowershell
$secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
```

## <a name="step-5-reference-your-self-signed-certificates-url-while-creating-a-vm"></a>步驟 5：在建立 VM 時參考您的自我簽署憑證的 URL
#### <a name="azure-resource-manager-templates"></a>Azure Resource Manager 範本
透過範本建立 VM 時，憑證會在密碼區段和 winRM 區段中參考，如下所示：

```json
"osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "<resource id of the Key Vault containing the secret>"
          },
          "vaultCertificates": [
            {
              "certificateUrl": "<URL for the certificate you got in Step 4>",
              "certificateStore": "<Name of the certificate store on the VM>"
            }
          ]
        }
      ],
      "windowsConfiguration": {
        ...
        "winRM": {
          "listeners": [
            {
              "protocol": "http"
            },
            {
              "protocol": "https",
              "certificateUrl": "<URL for the certificate you got in Step 4>"
            }
          ]
        },
        ...
      }
    },
```

上述的範例範本可以在這裡找到： [201-vm-winrm-keyvault-windows](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows)

此範本的原始程式碼位於 [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)

#### <a name="powershell"></a>PowerShell
```azurepowershell
$vm = New-AzVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
$credential = Get-Credential
$secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
$vm = Set-AzVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -ProvisionVMAgent -WinRMCertificateUrl $secretURL
$sourceVaultId = (Get-AzKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
$CertificateStore = "My"
$vm = Add-AzVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL
```

## <a name="step-6-connecting-to-the-vm"></a>步驟 6︰連接到 VM
在您可以連接至 VM 之前，您必須確定您的電腦已設定 WinRM 遠端管理。 以系統管理員身分啟動 PowerShell 並執行下列命令來確認您已設定完畢。

```azurepowershell
Enable-PSRemoting -Force
```

> [!NOTE]
> 如果上述程式碼無法運作，您可能需要確定 WinRM 服務正在執行。 您可以使用 `Get-Service WinRM`
> 
> 

安裝完成之後，您可以使用下列命令連接到 VM

```azurepowershell
Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate
```
