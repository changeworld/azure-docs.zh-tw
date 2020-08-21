---
title: 從 Azure Key Vault 匯出憑證
description: 從 Azure Key Vault 匯出憑證
services: key-vault
author: sebansal
tags: azure-key-vault
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.custom: mvc, devx-track-azurecli
ms.date: 08/11/2020
ms.author: sebansal
ms.openlocfilehash: afab65b22d9487f30da458346bf143a557bec0d8
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588887"
---
# <a name="export-certificate-from-azure-key-vault"></a>從 Azure Key Vault 匯出憑證

Azure Key Vault 可讓您輕鬆地布建、管理及部署網路的數位憑證，以及啟用應用程式的安全通訊。 如需更多有關憑證的一般資訊，請參閱 [Azure Key Vault 憑證](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates)

## <a name="about-azure-key-vault-certificate"></a>關於 Azure Key Vault 憑證

### <a name="composition-of-certificate"></a>憑證的構成要素
建立 Key Vault 憑證時，可定址的金鑰和秘密也會以相同名稱建立。 Key Vault 金鑰可讓金鑰作業和 Key Vault 秘密允許擷取憑證值作為秘密。 Key Vault 憑證也會包含公用 x509 憑證的中繼資料。 [閱讀更多資訊](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#composition-of-a-certificate)

### <a name="exportable-or-non-exportable-keys"></a>可匯出或不可匯出金鑰
Key Vault 憑證建立後，該憑證將可透過可定址秘密和 PFX 或 PEM 格式的私密金鑰來擷取。 用來建立憑證的原則必須指出金鑰是可匯出的。 如果原則指出金鑰是不可匯出的，則擷取為祕密的值不會包含私密金鑰。

支援金鑰類型有兩種 – 具有憑證的 RSA  或 RSA HSM  。 可匯出金鑰允許使用 RSA，不支援 RSA HSM。 [閱讀更多資訊](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#exportable-or-non-exportable-key)

您可以使用 Azure CLI、PowerShell 或入口網站匯出 Azure Key Vault 中的預存憑證。

> [!NOTE]
> 請務必注意，您只需要在金鑰保存庫中匯入憑證的密碼。 Key Vault 不會儲存相關聯的密碼，因此當您匯出憑證時，密碼會是空白的。

## <a name="exporting-certificate-using-cli"></a>使用 CLI 匯出憑證
下列命令可讓您下載 Key Vault 憑證的**公開部分**。

```azurecli
az keyvault certificate download --file
                                 [--encoding {DER, PEM}]
                                 [--id]
                                 [--name]
                                 [--subscription]
                                 [--vault-name]
                                 [--version]
```
如需範例和參數定義，請[檢視這裡](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-download)



如果您要下載整個憑證，亦即**組成的公開和私人部分**，可以下載憑證作為祕密來完成。

```azurecli
az keyvault secret download –file {nameofcert.pfx}
                            [--encoding {ascii, base64, hex, utf-16be, utf-16le, utf-8}]
                            [--id]
                            [--name]
                            [--subscription]
                            [--vault-name]
                            [--version]
```
如需參數定義，請[檢視這裡](https://docs.microsoft.com/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-download)


## <a name="exporting-certificate-using-powershell"></a>使用 PowerShell 匯出憑證

此命令會從名為 ContosoKV01 的金鑰保存庫取得名為 TestCert01 的憑證。 若要將憑證下載為 pfx 檔案，請執行下列命令。 這些命令會存取 SecretId，然後將內容儲存為 pfx 檔案。

```azurepowershell
$cert = Get-AzKeyVaultCertificate -VaultName "ContosoKV01" -Name "TestCert01"
$kvSecret = Get-AzKeyVaultSecret -VaultName "ContosoKV01" -Name $Cert.Name
$kvSecretBytes = [System.Convert]::FromBase64String($kvSecret.SecretValueText)
$certCollection = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2Collection
$certCollection.Import($kvSecretBytes,$null,[System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable)
$password = '******'
$protectedCertificateBytes = $certCollection.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $password)
$pfxPath = [Environment]::GetFolderPath("Desktop") + "\MyCert.pfx"
[System.IO.File]::WriteAllBytes($pfxPath, $protectedCertificateBytes)
```
這會匯出具有私密金鑰的整個憑證鏈，而此憑證將會受到密碼保護。
如需有關 ```Get-AzKeyVaultCertificate``` 命令和參數的詳細資訊，請參閱[範例 2](https://docs.microsoft.com/powershell/module/az.keyvault/Get-AzKeyVaultCertificate?view=azps-4.4.0)

## <a name="exporting-certificate-using-portal"></a>使用入口網站匯出憑證

在入口網站上，當您在憑證刀鋒視窗中建立/匯入憑證時，會收到已成功建立憑證的通知。 選取憑證時，您可以按一下目前的版本，即可看到下載選項。


按一下 [以 CER 格式下載] 或 [以 PFX/PEM 格式下載] 按鈕即可下載憑證。


![憑證下載](../media/certificates/quick-create-portal/current-version-shown.png)


## <a name="exporting-app-service-certificate-from-key-vault"></a>將 App Service 憑證從金鑰保存庫中匯出

Azure App Service 憑證提供便利的方式來購買 SSL 憑證，並可直接從入口網站指派給 Azure 應用程式。 這些憑證也可以從入口網站匯出為 PFX 檔案，以便在其他地方使用。
匯入之後，應用程式服務憑證可能**位於祕密之下**。

如需匯出應用程式服務憑證的步驟，請[閱讀本文](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx)

## <a name="read-more"></a>閱讀更多資訊
* [各種憑證檔案類型和定義](https://docs.microsoft.com/archive/blogs/kaushal/various-ssltls-certificate-file-typesextensions)