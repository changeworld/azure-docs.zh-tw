---
title: 使用批次處理安全地訪問金鑰保存庫 - Azure 批次處理
description: 瞭解如何使用 Azure 批次處理從金鑰保存庫以程式設計方式訪問憑據。
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.workload: big-compute
ms.topic: article
ms.date: 02/13/2020
ms.author: lahugh
ms.openlocfilehash: 0134e7d92ddca9bd3b45abaf642f33de9d209b33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78192297"
---
# <a name="securely-access-key-vault-with-batch"></a>使用 Batch 安全地存取 Key Vault

在本文中，您將瞭解如何設置 Batch-節點以安全地訪問存儲在 Azure 金鑰保存庫中的憑據。 將管理員憑據放在金鑰保存庫中，然後硬編碼憑據以從腳本訪問金鑰保存庫是沒有意義的。 解決方案是使用授予批次處理節點對金鑰保存庫存取權限的證書。 通過幾個步驟，我們可以為 Batch 實現安全金鑰存儲。

要從批次處理節點對 Azure 金鑰保存庫進行身份驗證，您需要：

- Azure 活動目錄 （Azure AD） 憑據
- 證書
- 批次帳戶
- 具有至少一個節點的批次處理池

## <a name="obtain-a-certificate"></a>取得憑證

如果還沒有證書，則獲取證書的最簡單方法是使用`makecert`命令列工具生成自簽章憑證。

您通常可以在此`makecert`路徑中找到： `C:\Program Files (x86)\Windows Kits\10\bin\<arch>`。 以管理員身份打開命令提示，並使用以下示例`makecert`導航到。

```console
cd C:\Program Files (x86)\Windows Kits\10\bin\x64
```

接下來，使用`makecert`該工具創建名為`batchcertificate.cer`和`batchcertificate.pvk`的自簽章憑證檔。 使用的通用名稱 （CN） 對於此應用程式並不重要，但將其製作出能夠告訴您證書用途的內容會很有説明。

```console
makecert -sv batchcertificate.pvk -n "cn=batch.cert.mydomain.org" batchcertificate.cer -b 09/23/2019 -e 09/23/2019 -r -pe -a sha256 -len 2048
```

批次處理需要檔`.pfx`。 使用[pvk2pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx)工具將`.cer`和`.pvk`創建`makecert`的檔轉換為單個`.pfx`檔。

```console
pvk2pfx -pvk batchcertificate.pvk -spc batchcertificate.cer -pfx batchcertificate.pfx -po
```

## <a name="create-a-service-principal"></a>建立服務主體

對金鑰保存庫的訪問授予**使用者**或服務**主體**。 要以程式設計方式訪問金鑰保存庫，請使用具有我們創建上一步步驟的證書的服務主體。

有關 Azure 服務主體的詳細資訊，請參閱[Azure 活動目錄中的應用程式和服務主體物件](../active-directory/develop/app-objects-and-service-principals.md)。

> [!NOTE]
> 服務主體必須與金鑰保存庫位於同一 Azure AD 租戶中。

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

應用程式的 URL 並不重要，因為我們只將它們用於金鑰保存庫訪問。

## <a name="grant-rights-to-key-vault"></a>授予金鑰保存庫的許可權

在上一步驟中創建的服務主體需要從金鑰保存庫檢索機密的許可權。 可以通過 Azure 門戶或下面的 PowerShell 命令授予許可權。

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'BatchVault' -ServicePrincipalName '"https://batch.mydomain.com' -PermissionsToSecrets 'Get'
```

## <a name="assign-a-certificate-to-a-batch-account"></a>將證書分配給批次處理帳戶

創建 Batch 池，然後轉到池中的證書選項卡並分配您創建的證書。 證書現在位於所有批次處理節點上。

接下來，我們需要將證書分配給 Batch 帳戶。 將證書分配給帳戶允許將其分配給池，然後分配給節點。 最簡單的方法是轉到門戶中的 Batch 帳戶，導航到**證書**，然後選擇 **"添加**"。 上傳我們在`.pfx`[獲取證書](#obtain-a-certificate)中生成的檔並提供密碼。 完成後，證書將添加到清單中，您可以驗證指紋。

現在，在創建 Batch 池時，可以導航到池中的**證書**，並將創建的證書分配給該池。 執行此操作時，請確保為商店位置選擇**本地電腦**。 證書載入在池中的所有 Batch-節點上。

## <a name="install-azure-powershell"></a>安裝 Azure PowerShell

如果計畫使用節點上的 PowerShell 腳本訪問金鑰保存庫，則需要安裝 Azure PowerShell 庫。 有幾種方法可以做到這一點，如果您的節點安裝了 Windows 管理框架 （WMF） 5，則可以使用安裝模組命令來下載它。 如果使用的節點沒有 WMF 5，則安裝它的最簡單方法是將 Azure PowerShell`.msi`檔與 Batch 檔捆綁在一起，然後將安裝程式稱為 Batch 啟動腳本的第一部分。 有關詳細資訊，請參閱此示例：

```powershell
$psModuleCheck=Get-Module -ListAvailable -Name Azure -Refresh
if($psModuleCheck.count -eq 0) {
    $psInstallerPath = Join-Path $downloadPath "azure-powershell.3.4.0.msi" Start-Process msiexec.exe -ArgumentList /i, $psInstallerPath, /quiet -wait
}
```

## <a name="access-key-vault"></a>存取 Key Vault

現在，我們都設置在批次處理節點上運行的腳本中訪問金鑰保存庫。 要從腳本訪問金鑰保存庫，只需讓腳本使用證書對 Azure AD 進行身份驗證。 要在 PowerShell 中執行此操作，請使用以下示例命令。 為**指紋**、**應用 ID（** 服務主體的 ID）和**租戶 ID（** 服務主體所在的租戶）指定相應的 GUID。

```powershell
Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint -ApplicationId
```

經過身份驗證後，可以正常訪問 KeyVault。

```powershell
$adminPassword=Get-AzureKeyVaultSecret -VaultName BatchVault -Name batchAdminPass
```

這些是要在腳本中使用的憑據。
