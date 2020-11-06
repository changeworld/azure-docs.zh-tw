---
title: 使用 Batch 安全地存取 Key Vault
description: 了解如何使用 Azure Batch，以程式設計方式從 Key Vault 存取您的認證。
ms.topic: how-to
ms.date: 10/28/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 25cb05374fc0667306e2b1004b3cd237413b4409
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "94337486"
---
# <a name="securely-access-key-vault-with-batch"></a>使用 Batch 安全地存取 Key Vault

在本文中，您將瞭解如何設定 Batch-節點，以安全地存取儲存在 [Azure Key Vault](../key-vault/general/overview.md)中的認證。 將您的系統管理員認證放入 Key Vault 中，然後再將認證寫入程式碼，以便從指令碼存取 Key Vault 是沒有意義的。 解決方案是使用對 Key Vault 授與 Batch 節點存取權的憑證。

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

Batch 需要 `.pfx` 檔案。 使用 [pvk2pfx](/windows-hardware/drivers/devtest/pvk2pfx) 工具，將 `makecert` 所建立的 `.cer` 和 `.pvk` 檔案轉換成單一 `.pfx` 檔案。

```console
pvk2pfx -pvk batchcertificate.pvk -spc batchcertificate.cer -pfx batchcertificate.pfx -po
```

## <a name="create-a-service-principal"></a>建立服務主體

Key Vault 的存取權會授與 **使用者** 或 **服務主體** 。 若要以程式設計方式存取 Key Vault，請使用您在上一個步驟中建立之憑證的 [服務主體](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) 。 服務主體必須與 Key Vault 位於相同的 Azure AD 租用戶中。

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

應用程式的 Url 並不重要，因為我們只使用它們來進行 Key Vault 存取。

## <a name="grant-rights-to-key-vault"></a>將權限授與 Key Vault

在上一個步驟中建立的服務主體需要從 Key Vault 擷取密碼的權限。 您可以透過 [Azure 入口網站](/azure/key-vault/general/assign-access-policy-portal) 或使用下列 PowerShell 命令來授與許可權。

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'BatchVault' -ServicePrincipalName '"https://batch.mydomain.com' -PermissionsToSecrets 'Get'
```

## <a name="assign-a-certificate-to-a-batch-account"></a>將憑證指派給 Batch 帳戶

建立 Batch 集區，然後移至集區中的 [憑證] 索引標籤，並指派您所建立的憑證。 憑證現在已在所有 Batch 節點上。

接下來，將憑證指派給 Batch 帳戶。 將憑證指派給帳戶，可讓 Batch 將它指派給集區，然後再指派給節點。 若要這麼做，最簡單的方式是在入口網站中移至您的 Batch 帳戶、瀏覽至 [憑證]，然後選取 [新增]。 上傳 `.pfx` 您稍早產生的檔案，並提供密碼。 完成後，憑證就會新增至清單，而且您可以驗證指紋。

現在當您建立 Batch 集區時，您可以流覽至集區內的 **憑證** ，並將您建立的憑證指派給該集區。 當您這麼做時，請確定您選取的是存放區位置的 **LocalMachine** 。 憑證會載入到集區中的所有 Batch 節點上。

## <a name="install-azure-powershell"></a>安裝 Azure PowerShell

如果您打算使用節點上的 PowerShell 指令碼存取 Key Vault，則需要安裝 Azure PowerShell 程式庫。 如果您的節點已安裝 Windows Management Framework (WMF) 5，則可以使用 install 模組命令來下載它。 如果您使用的節點沒有 WMF 5，安裝它的最簡單方式就是將 Azure PowerShell 檔案 `.msi` 與您的批次檔配套，然後呼叫安裝程式做為批次啟動腳本的第一個部分。 如需詳細資訊，請參閱此範例：

```powershell
$psModuleCheck=Get-Module -ListAvailable -Name Azure -Refresh
if($psModuleCheck.count -eq 0) {
    $psInstallerPath = Join-Path $downloadPath "azure-powershell.3.4.0.msi" Start-Process msiexec.exe -ArgumentList /i, $psInstallerPath, /quiet -wait
}
```

## <a name="access-key-vault"></a>存取 Key Vault

現在您已準備好存取在 Batch-節點上執行的腳本 Key Vault。 若要從指令碼存取 Key Vault，您只需要針對使用憑證的 Azure AD 驗證指令碼。 若要在 PowerShell 中執行此操作，請使用下列範例命令。 為 [指紋]、[應用程式識別碼] (服務主體的識別碼)，以及 [租用戶識別碼] (您服務主體所在的租用戶) 指定適當的 GUID。

```powershell
Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint -ApplicationId
```

經過驗證之後，您就可以像平常一樣存取 KeyVault。

```powershell
$adminPassword=Get-AzureKeyVaultSecret -VaultName BatchVault -Name batchAdminPass
```

這些是要在您指令碼中使用的認證。

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [Azure Key Vault](../key-vault/general/overview.md)。
- 檢查 [Batch 的 Azure 安全性基準](security-baseline.md)。
- 瞭解 Batch 功能，例如[使用 Linux 計算節點](batch-linux-nodes.md)[來設定計算節點的存取權](pool-endpoint-configuration.md)，以及[使用私人端點](private-connectivity.md)。