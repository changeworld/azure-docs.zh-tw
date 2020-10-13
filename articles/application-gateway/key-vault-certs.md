---
title: 使用 Azure Key Vault 憑證的 TLS 終止
description: 了解 Azure 應用程式閘道如何與 Key Vault 整合，以使用連結到 HTTPS 所支援接聽程式的伺服器憑證。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 07/27/2020
ms.author: victorh
ms.openlocfilehash: e7c4842494c144f5cd64d46f53f7a99266064680
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91993650"
---
# <a name="tls-termination-with-key-vault-certificates"></a>使用 Key Vault 憑證的 TLS 終止

[Azure Key Vault](../key-vault/general/overview.md) 是平台管理的祕密存放區，可讓您用來保護祕密、金鑰和 TLS/SSL 憑證。 Azure 應用程式閘道支援與 Key Vault 整合，以使用連結到 HTTPS 所支援接聽程式的伺服器憑證。 這項支援僅限於應用程式閘道的 v2 SKU。

Key Vault 整合提供兩種模型來進行 TLS 終止：

- 您可以明確地提供附加至接聽程式的 TLS/SSL 憑證。 此模型是將 TLS/SSL 憑證傳遞至應用程式閘道以進行 TLS 終止的傳統方式。
- 當您建立啟用 HTTPS 的接聽程式時，可以選擇性地提供現有 Key Vault 憑證或秘密的參考。

應用程式閘道與 Key Vault 的整合提供許多優點，包括：

- 更強的安全性，因為 TLS/SSL 憑證不是由應用程式開發小組直接處理。 整合可讓不同的安全性小組：
  * 設定應用程式閘道。
  * 控制應用程式閘道生命週期。
  * 將許可權授與選取的應用程式閘道，以存取儲存在金鑰保存庫中的憑證。
- 支援將現有的憑證匯入到您的金鑰保存庫。 或使用 Key Vault Api 來建立和管理任何信任 Key Vault 夥伴的新憑證。
- 支援自動更新儲存在金鑰保存庫中的憑證。

應用程式閘道目前僅支援軟體驗證的憑證。 硬體安全性模組 (HSM) 驗證的憑證不受支援。 應用程式閘道設定為使用 Key Vault 憑證之後，其實例會從 Key Vault 取出憑證，並將其安裝在本機以進行 TLS 終止。 實例也會以4小時的間隔輪詢 Key Vault，以取得憑證的更新版本（如果有的話）。 如果找到更新的憑證，則會自動輪替目前與 HTTPS 接聽程式相關聯的 TLS/SSL 憑證。

> [!NOTE]
> Azure 入口網站只支援 KeyVault 憑證，而不支援秘密。 應用程式閘道仍然支援從 KeyVault 參考秘密，但只能透過非入口網站資源，例如 PowerShell、CLI、API、ARM 範本等等。 

## <a name="how-integration-works"></a>整合的運作方式

應用程式閘道與 Key Vault 的整合需要三個步驟的設定程式：

1. **建立使用者指派的受控識別**

   您可以建立或重複使用現有的使用者指派受控識別，應用程式閘道會使用此身分識別來代表您從 Key Vault 取出憑證。 如需詳細資訊，請參閱 [使用 Azure 入口網站建立、列出、刪除或指派角色給使用者指派的受控識別](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。 此步驟會在 Azure Active Directory 租使用者中建立新的身分識別。 身分識別是由用來建立身分識別的訂用帳戶所信任。

1. **設定您的金鑰保存庫**

   然後，您可以匯入現有的憑證，或在金鑰保存庫中建立新的憑證。 透過應用程式閘道執行的應用程式將會使用此憑證。 在此步驟中，您也可以使用儲存為無密碼、base-64 編碼的 PFX 檔案的金鑰保存庫秘密。 基於金鑰保存庫中的憑證類型物件所提供的自動更新功能，我們建議使用憑證類型。 建立憑證或密碼之後，您可以在金鑰保存庫中定義存取原則，以允許授與身分識別存取秘密*的許可權。*
   
   > [!NOTE]
   > 如果您透過 ARM 範本部署應用程式閘道（使用 Azure CLI 或 PowerShell），或透過從 Azure 入口網站部署的 Azure 應用程式，則 SSL 憑證會以 base64 編碼的 PFX 檔案儲存在金鑰保存庫中。 您必須完成「使用 Azure Key Vault 中的步驟 [，以在部署期間傳遞安全的參數值](../azure-resource-manager/templates/key-vault-parameter.md)。 
   >
   > 請特別注意，請務必將設定 `enabledForTemplateDeployment` 為 `true` 。 憑證可能會無密碼或可能有密碼。 在具有密碼的憑證案例中，下列範例會顯示 `sslCertificates` `properties` 適用于應用程式閘道的 ARM 範本設定中專案的可能設定。 和的值 `appGatewaySSLCertificateData` `appGatewaySSLCertificatePassword` 是從金鑰保存庫查閱，如「 [使用動態識別碼參考秘密](../azure-resource-manager/templates/key-vault-parameter.md#reference-secrets-with-dynamic-id)」一節中所述。 從反向參考參考 `parameters('secretName')` ，以查看如何執行查閱。 如果憑證是無密碼，請省略此 `password` 專案。
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

   ![Key vault 憑證](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>接下來的步驟

[使用 Azure PowerShell Key Vault 憑證設定 TLS 終止](configure-keyvault-ps.md)
