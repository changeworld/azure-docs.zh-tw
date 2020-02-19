---
title: 使用批次 Azure Batch 安全地存取 Key Vault
description: 瞭解如何使用 Azure Batch 以程式設計方式從 Key Vault 存取您的認證。
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.workload: big-compute
ms.topic: article
ms.date: 02/13/2020
ms.author: lahugh
ms.openlocfilehash: 14cbacf43e83dc768e9a85620df131533b746671
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2020
ms.locfileid: "77463098"
---
# <a name="securely-access-key-vault-with-batch"></a>使用 Batch 安全地存取 Key Vault

在本文中，您將瞭解如何設定 Batch-節點，以安全地存取儲存在 Azure Key Vault 中的認證。 在 Key Vault 中找不到您的系統管理員認證，然後再將認證硬式編碼，以從腳本存取 Key Vault。 解決方案是使用憑證來授與 Batch-節點 Key Vault 的存取權。 只要幾個步驟，我們就可以為 Batch 執行安全金鑰儲存。

若要向 Batch-節點驗證 Azure Key Vault，您需要：

- Azure Active Directory （Azure AD）認證
- 憑證
- Batch 帳戶
- 至少具有一個節點的 Batch 集區

## <a name="obtain-a-certificate"></a>取得憑證

如果您還沒有憑證，最簡單的方法就是使用 `makecert` 命令列工具來產生自我簽署憑證。

您通常可以在此路徑中找到 `makecert`： `C:\Program Files (x86)\Windows Kits\10\bin\<arch>`。 以系統管理員身分開啟命令提示字元，並使用下列範例流覽至 `makecert`。

```console
cd C:\Program Files (x86)\Windows Kits\10\bin\x64
```

接下來，使用 `makecert` 工具來建立自我簽署的憑證檔案，稱為 `batchcertificate.cer` 和 `batchcertificate.pvk`。 使用的一般名稱（CN）對此應用程式而言並不重要，但讓它成為可告訴您憑證用途的內容會很有説明。

```console
makecert -sv batchcertificate.pvk -n "cn=batch.cert.mydomain.org batchcertificate.cer -b 09/23/2019 -e 09/23/2019 -r -pe -a sha256 -len 2048
```

Batch 需要 `.pfx` 檔案。 使用[pvk2pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx)工具，將 `makecert` 所建立的 `.cer` 和 `.pvk` 檔案轉換成單一 `.pfx` 檔案。

```console
pvk2pfx -pvk batchcertificate.pvk -spc batchcertificate.cer -pfx batchcertificate.pfx -po
```

## <a name="create-a-service-principal"></a>建立服務主體

Key Vault 的存取權授與**使用者**或**服務主體**。 若要以程式設計方式存取 Key Vault，請使用服務主體搭配我們先前建立的憑證。

如需有關 Azure 服務主體的詳細資訊，請參閱[Azure Active Directory 中的應用程式和服務主體物件](../active-directory/develop/app-objects-and-service-principals.md)。

> [!NOTE]
> 服務主體必須與 Key Vault 位於相同的 Azure AD 租使用者中。

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

應用程式的 Url 並不重要，因為我們只會使用它們來 Key Vault 存取。

## <a name="grant-rights-to-key-vault"></a>授與 Key Vault 的許可權

在上一個步驟中建立的服務主體需要有從 Key Vault 取得密碼的許可權。 您可以透過 Azure 入口網站或使用下列 PowerShell 命令來授與許可權。

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'BatchVault' -ServicePrincipalName '"https://batch.mydomain.com' -PermissionsToSecrets 'Get'
```

## <a name="assign-a-certificate-to-a-batch-account"></a>將憑證指派給 Batch 帳戶

建立 Batch 集區，然後移至集區中的 [憑證] 索引標籤，並指派您所建立的憑證。 憑證現在已在所有 Batch-節點上。

接下來，我們需要將憑證指派給 Batch 帳戶。 將憑證指派給帳戶，可讓我們將它指派給集區，然後指派給節點。 若要這麼做，最簡單的方法是在入口網站中移至您的 Batch 帳戶，流覽至 [**憑證**]，然後選取 [**新增**]。 上傳我們在[取得憑證](#obtain-a-certificate)和提供密碼中產生的 `.pfx` 檔案。 完成後，憑證就會新增至清單，您可以驗證指紋。

現在，當您建立 Batch 集區時，您可以流覽至集區中的**憑證**，並將您建立的憑證指派給該集區。 當您這麼做時，請務必選取 [ **LocalMachine** ] 做為存放區位置。 憑證會載入集區中的所有 Batch-節點上。

## <a name="install-azure-powershell"></a>安裝 Azure PowerShell

如果您打算使用節點上的 PowerShell 腳本來存取 Key Vault，則需要安裝 Azure PowerShell 程式庫。 有幾種方式可以執行此動作，如果您的節點已安裝 Windows Management Framework （WMF）5，則您可以使用 install 模組命令來下載它。 如果您使用沒有 WMF 5 的節點，最簡單的安裝方式就是將 Azure PowerShell `.msi` 檔案與您的批次檔配套，然後呼叫安裝程式做為批次啟動腳本的第一個部分。 如需詳細資訊，請參閱此範例：

```powershell
$psModuleCheck=Get-Module -ListAvailable -Name Azure -Refresh
if($psModuleCheck.count -eq 0) {
    $psInstallerPath = Join-Path $downloadPath "azure-powershell.3.4.0.msi" Start-Process msiexec.exe -ArgumentList /i, $psInstallerPath, /quiet -wait
}
```

## <a name="access-key-vault"></a>存取 Key Vault

現在，我們已準備好在批次節點上執行的腳本中存取 Key Vault。 若要從腳本存取 Key Vault，您只需要針對使用憑證的 Azure AD 驗證腳本。 若要在 PowerShell 中執行這項操作，請使用下列範例命令。 針對 [**指紋**]、[**應用程式識別碼**] （服務主體的識別碼）和 [**租使用者識別碼**] （您的服務主體所在的租使用者）指定適當的 GUID。

```powershell
Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint -ApplicationId
```

經過驗證之後，您就可以像平常一樣存取 KeyVault。

```powershell
$adminPassword=Get-AzureKeyVaultSecret -VaultName BatchVault -Name batchAdminPass
```

這些是要在您的腳本中使用的認證。
