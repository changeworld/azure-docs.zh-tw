---
title: 在 Windows 叢集上設定加密憑證
description: 了解如何設定加密憑證，並在 Windows 叢集上將祕密加密。
ms.topic: conceptual
ms.date: 01/04/2019
ms.openlocfilehash: eb4909d62a2627c368f24dab572b25c6f1df30ec
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82583283"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-windows-clusters"></a>設定加密憑證，並在 Windows 叢集上將祕密加密
本文書名如何設定加密憑證，並在 Windows 叢集上將祕密加密。 對於 Linux 叢集，請參閱[在 Linux 叢集上設定加密憑證並將祕密加密。][secret-management-linux-specific-link]

[Azure Key Vault][key-vault-get-started] 在此是當做憑證的安全儲存位置，以及讓憑證安裝在 Azure 中的 Service Fabric 叢集上的方法。 如果您沒有要部署至 Azure，您不需要使用金鑰保存庫管理 Service Fabric 應用程式中的密碼。 不過，在應用程式中*使用*秘密與雲端平臺無關，可讓應用程式部署到裝載于任何位置的叢集。 

## <a name="obtain-a-data-encipherment-certificate"></a>取得資料加密憑證
資料編密憑證只會用於服務的 Settings.xml 中[參數][parameters-link]，以及服務的 ServiceManifest.xml 中[環境變數][environment-variables-link]的加密與解密。 它不是用來驗證或簽署密碼文字。 憑證必須符合下列要求：

* 憑證必須包含私密金鑰。
* 憑證必須是為了進行金鑰交換而建立，且可匯出成個人資訊交換檔 (.pfx)。
* 憑證的金鑰使用法必須包含資料編密 (10)，而且不應該包含伺服器驗證或用戶端驗證。 
  
  例如，當使用 PowerShell 建立自我簽署的憑證時，`KeyUsage` 旗標必須設定為 `DataEncipherment`：
  
  ```powershell
  New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
  ```

## <a name="install-the-certificate-in-your-cluster"></a>在叢集中安裝憑證
此憑證必須安裝在叢集中的每個節點上。 請參閱[如何使用 Azure Resource Manager][service-fabric-cluster-creation-via-arm] 建立叢集的安裝指示。 

## <a name="encrypt-application-secrets"></a>加密應用程式密碼
下列 PowerShell 命令會用來加密密碼。 此命令只會將值加密；並**不會**簽署密碼文字。 您必須使用安裝在叢集中相同的編密憑證，以產生密碼值的加密文字︰

```powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

產生的 Base-64 編碼字串同時包含密碼的加密文字，以及用來對其加密的憑證相關資訊。

## <a name="next-steps"></a>後續步驟
了解如何[指定應用程式中已加密的祕密][secret-management-specify-encrypted-secrets-link]。

<!-- Links -->
[key-vault-get-started]:../key-vault/general/overview.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
