---
title: 使用 Batch 安全地存取 Key Vault
description: 了解如何使用 Azure Batch，以程式設計方式從 Key Vault 存取您的認證。
ms.topic: how-to
ms.date: 02/13/2020
ms.openlocfilehash: 3d0b2128bef1434f073700eb83e5935d74d8bb7a
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725715"
---
# <a name="securely-access-key-vault-with-batch"></a>使用 Batch 安全地存取 Key Vault

在此文章中，您將了解如何設定 Batch 節點，以便安全地存取儲存在 Azure Key Vault 中的認證。 將您的系統管理員認證放入 Key Vault 中，然後再將認證寫入程式碼，以便從指令碼存取 Key Vault 是沒有意義的。 解決方案是使用對 Key Vault 授與 Batch 節點存取權的憑證。 只要幾個步驟，我們就可以為 Batch 實作安全金鑰儲存。

若要從 Batch 節點向 Azure Key Vault 進行驗證，您需要：

- Azure Active Directory (Azure AD) 認證
- 憑證
- Batch 帳戶
- 至少具有一個節點的 Batch 集區

## <a name="obtain-a-certificate"></a>取得憑證

如果您還沒有憑證，使用 `makecert` 命令列工具產生一個自我簽署憑證是取得憑證最簡單的方式。

您通常可以在下列路徑中找到 `makecert`：`C:\Program Files (x86)\Windows Kits\10\bin\<arch>`。 以系統管理員身分開啟命令提示字元，並使用下列範例瀏覽至 `makecert`。

```console
cd C:\Program Files (x86)\Windows Kits\10\bin\x64
```

接下來，使用 `makecert` 工具建立自我簽署憑證檔案，稱為 `batchcertificate.cer` 與 `batchcertificate.pvk`。 使用的一般名稱 (CN) 對此應用程式而言並不重要，但讓該名稱告訴您憑證用途時，就會變得很重要。

```console
makecert -sv batchcertificate.pvk -n "cn=batch.cert.mydomain.org" batchcertificate.cer -b 09/23/2019 -e 09/23/2019 -r -pe -a sha256 -len 2048
```

Batch 需要 `.pfx` 檔案。 使用 [pvk2pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx) 工具，將 `makecert` 所建立的 `.cer` 和 `.pvk` 檔案轉換成單一 `.pfx` 檔案。

```console
pvk2pfx -pvk batchcertificate.pvk -spc batchcertificate.cer -pfx batchcertificate.pfx -po
```

## <a name="create-a-service-principal"></a>建立服務主體

Key Vault 的存取權會授與**使用者**或**服務主體**。 若要以程式設計方式存取 Key Vault，請使用服務主體搭配我們在上一個步驟中建立的憑證。

如需有關 Azure 服務主體的詳細資訊，請參閱 [Azure Active Directory 中的應用程式和服務主體物件](../active-directory/develop/app-objects-and-service-principals.md)。

> [!NOTE]
> 服務主體必須與 Key Vault 位於相同的 Azure AD 租用戶中。

```powershell
$now = [System.DateTime]::Parse("2020-02-10")
# Set this to the expiration date of the certificate
$expirationDate = [System.DateTime]::Parse("2021-02-10")
# Point the script at the cer file you created $cerCertificateFilePath = 'c:\temp\batchcertificate.cer'
$cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$cer.Import($cerCertificateFilePath)
# Load the certificate into memory
$credValue = [System.Convert]::ToBase64String($cer.GetRawCertData())
# Create a new AAD application that uses this certificate
$newADApplication = New-AzureRmADApplication -DisplayName "Batch Key Vault Access" -HomePage "https://batch.mydomain.com" -IdentifierUris "https://batch.mydomain.com" -certValue $credValue -StartDate $now -EndDate $expirationDate
# Create new AAD service principal that uses this application
$newAzureAdPrincipal = New-AzureRmADServicePrincipal -ApplicationId $newADApplication.ApplicationId
```

應用程式的 URL 並不重要，因為我們只會將其用於 Key Vault 存取。

## <a name="grant-rights-to-key-vault"></a>將權限授與 Key Vault

在上一個步驟中建立的服務主體需要從 Key Vault 擷取密碼的權限。 您可以透過 Azure 入口網站或使用下列 PowerShell 命令來授與權限。

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'BatchVault' -ServicePrincipalName '"https://batch.mydomain.com' -PermissionsToSecrets 'Get'
```

## <a name="assign-a-certificate-to-a-batch-account"></a>將憑證指派給 Batch 帳戶

建立 Batch 集區，然後移至集區中的 [憑證] 索引標籤，並指派您所建立的憑證。 憑證現在已在所有 Batch 節點上。

接下來，我們需要將憑證指派給 Batch 帳戶。 將憑證指派給帳戶可讓我們將其指派給集區，然後指派給節點。 若要這麼做，最簡單的方式是在入口網站中移至您的 Batch 帳戶、瀏覽至 [憑證]，然後選取 [新增]。 上傳我們在[取得憑證](#obtain-a-certificate)中產生的 `.pfx` 檔案，並提供密碼。 完成後，憑證就會新增至清單，而且您可以驗證指紋。

現在，當您建立 Batch 集區時，可以瀏覽至集區中的 [憑證]，並將您建立的憑證指派給該集區。 當您這麼做時，請確定您選取的是存放區位置的 **LocalMachine**。 憑證會載入到集區中的所有 Batch 節點上。

## <a name="install-azure-powershell"></a>安裝 Azure PowerShell

如果您打算使用節點上的 PowerShell 指令碼存取 Key Vault，則需要安裝 Azure PowerShell 程式庫。 方式有幾種，如果您的節點已安裝 Windows Management Framework (WMF) 5，則您可以使用 install-module 命令加以下載。 如果您使用沒有 WMF 5 的節點，最簡單的安裝方式就是將 Azure PowerShell `.msi` 檔案與您的 Batch 檔案組合在一起，然後呼叫安裝程式，作為 Batch 啟動指令碼的第一個部分。 如需詳細資訊，請參閱此範例：

```powershell
$psModuleCheck=Get-Module -ListAvailable -Name Azure -Refresh
if($psModuleCheck.count -eq 0) {
    $psInstallerPath = Join-Path $downloadPath "azure-powershell.3.4.0.msi" Start-Process msiexec.exe -ArgumentList /i, $psInstallerPath, /quiet -wait
}
```

## <a name="access-key-vault"></a>存取 Key Vault

現在，我們已經全部設定完成，可以在 Batch 節點上執行的指令碼中存取 Key Vault。 若要從指令碼存取 Key Vault，您只需要針對使用憑證的 Azure AD 驗證指令碼。 若要在 PowerShell 中執行此操作，請使用下列範例命令。 為 [指紋]、[應用程式識別碼] (服務主體的識別碼)，以及 [租用戶識別碼] (您服務主體所在的租用戶) 指定適當的 GUID。

```powershell
Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint -ApplicationId
```

經過驗證之後，您就可以像平常一樣存取 KeyVault。

```powershell
$adminPassword=Get-AzureKeyVaultSecret -VaultName BatchVault -Name batchAdminPass
```

這些是要在您指令碼中使用的認證。
