---
title: Azure Key Vault 憑證的 TLS 終止
description: 瞭解如何將 Azure 應用程式閘道與連結到啟用 HTTPS 的接聽程式之伺服器憑證的 Key Vault 整合。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 780f2774cb37e3d6d43ed5137c29119c0f63fd0a
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2020
ms.locfileid: "82743703"
---
# <a name="tls-termination-with-key-vault-certificates"></a>Key Vault 憑證的 TLS 終止

[Azure Key Vault](../key-vault/general/overview.md)是平臺管理的秘密存放區，可讓您用來保護秘密、金鑰和 TLS/SSL 憑證。 Azure 應用程式閘道支援與 Key Vault 整合連接到已啟用 HTTPS 之接聽程式的伺服器憑證。 這項支援僅限於應用程式閘道的 v2 SKU。

Key Vault 整合為 TLS 終止提供兩種模型：

- 您可以明確地提供附加至接聽程式的 TLS/SSL 憑證。 此模型是將 TLS/SSL 憑證傳遞給應用程式閘道以進行 TLS 終止的傳統方式。
- 當您建立具備 HTTPS 功能的接聽程式時，您可以選擇性地提供現有 Key Vault 憑證或密碼的參考。

與 Key Vault 的應用程式閘道整合提供許多優點，包括：

- 較強的安全性，因為 TLS/SSL 憑證不是由應用程式開發小組直接處理。 整合可讓不同的安全性小組：
  * 設定應用程式閘道。
  * 控制應用程式閘道的生命週期。
  * 授與許可權給選取的應用程式閘道，以存取儲存在金鑰保存庫中的憑證。
- 支援將現有憑證匯入到您的金鑰保存庫。 或者，使用 Key Vault Api 來建立及管理任何受信任 Key Vault 夥伴的新憑證。
- 支援儲存在金鑰保存庫中的憑證自動更新。

應用程式閘道目前僅支援軟體驗證的憑證。 硬體安全模組（HSM）驗證的憑證不受支援。 在應用程式閘道設定為使用 Key Vault 憑證之後，其實例會從 Key Vault 抓取憑證，並將其安裝到本機以進行 TLS 終止。 實例也會依24小時間隔輪詢 Key Vault，以取得憑證的更新版本（如果有的話）。 如果找到更新的憑證，則會自動旋轉目前與 HTTPS 接聽程式相關聯的 TLS/SSL 憑證。

> [!NOTE]
> Azure 入口網站只支援 KeyVault 憑證，而非密碼。 應用程式閘道仍然支援從 KeyVault 參考秘密，但只能透過非入口網站資源（例如 PowerShell、CLI、API、ARM 範本等等）。 

## <a name="how-integration-works"></a>整合的運作方式

與 Key Vault 的應用程式閘道整合需要三個步驟的設定程式：

1. **建立使用者指派的受控識別**

   您建立或重複使用現有使用者指派的受控識別，應用程式閘道用來代表您取出 Key Vault 的憑證。 如需詳細資訊，請參閱[什麼是適用于 Azure 資源的受控識別？](../active-directory/managed-identities-azure-resources/overview.md)。 此步驟會在 Azure Active Directory 租使用者中建立新的身分識別。 身分識別受到用來建立身分識別的訂用帳戶所信任。

1. **設定您的金鑰保存庫**

   然後，您可以匯入現有的憑證，或在您的金鑰保存庫中建立一個新的。 透過應用程式閘道執行的應用程式將會使用此憑證。 在此步驟中，您也可以使用儲存為無密碼、以64編碼的 PFX 檔案的金鑰保存庫密碼。 基於金鑰保存庫中的憑證類型物件所提供的自動更新功能，我們建議使用憑證類型。 建立憑證或秘密之後，您必須在金鑰保存庫中定義存取原則，以*允許授與*身分識別存取秘密。
   
   > [!NOTE]
   > 如果您透過 ARM 範本部署應用程式閘道，不論是使用 Azure CLI 或 PowerShell，或是透過從 Azure 入口網站部署的 Azure 應用程式，SSL 憑證都會以 base64 編碼的 PFX 檔案形式儲存在金鑰保存庫中。 您必須完成使用 Azure Key Vault 中的步驟[，以在部署期間傳遞安全的參數值](../azure-resource-manager/templates/key-vault-parameter.md)。 
   >
   > 特別重要的是將設定`enabledForTemplateDeployment`為`true`。 憑證可能是無密碼的，或者它可能有密碼。 在具有密碼的憑證案例中，下列範例會顯示應用程式閘道的 ARM 範本`sslCertificates`設定中， `properties`專案的可能設定。 `appGatewaySSLCertificateData`和的值會`appGatewaySSLCertificatePassword`從金鑰保存庫查閱，如[參考具有動態識別碼的秘密](../azure-resource-manager/templates/key-vault-parameter.md#reference-secrets-with-dynamic-id)一節中所述。 請遵循中`parameters('secretName')`的反向參考，以查看查閱的發生方式。 如果憑證是無密碼，請省略該`password`專案。
   >   
   > ```
   > "sslCertificates": [
   >     {
   >         "name": "appGwSslCertificate",
   >         "properties": {
   >             "data": "[parameters('appGatewaySSLCertificateData')]",
   >             "password": "[parameters('appGatewaySSLCertificatePassword')]"
   >         }
   >     }
   > ]
   > ```

1. **設定應用程式閘道**

   完成上述兩個步驟之後，您可以設定或修改現有的應用程式閘道，以使用使用者指派的受控識別。 您也可以設定 HTTP 接聽程式的 TLS/SSL 憑證，以指向 Key Vault 憑證或秘密識別碼的完整 URI。

   ![金鑰保存庫憑證](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>後續步驟

[使用 Azure PowerShell 設定 Key Vault 憑證的 TLS 終止](configure-keyvault-ps.md)
