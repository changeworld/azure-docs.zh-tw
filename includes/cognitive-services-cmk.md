---
title: 包含檔案
description: Include 檔案
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.custom: include
ms.openlocfilehash: e5e1755e6351d308c041de5cefe943e9874c8ebd
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/14/2020
ms.locfileid: "79372408"
---
### <a name="enable-customer-managed-keys"></a>啟用客戶管理的金鑰

您必須使用 Azure Key Vault 來儲存客戶管理的金鑰。 您可以建立自己的金鑰，並將其儲存在金鑰保存庫中，或者您可以使用 Azure Key Vault Api 來產生金鑰。 認知服務資源和金鑰保存庫必須位於相同的區域和相同的 Azure Active Directory （Azure AD）租使用者中，但它們可以在不同的訂用帳戶中。 如需 Azure Key Vault 的詳細資訊，請參閱[什麼是 Azure Key Vault？](https://docs.microsoft.com/azure/key-vault/key-vault-overview)。

新的認知服務資源一律會使用 Microsoft 管理的金鑰進行加密。 建立資源時，不可能啟用客戶管理的金鑰。 客戶管理的金鑰會儲存在 Azure Key Vault 中，而金鑰保存庫必須布建存取原則，以將金鑰許可權授與與認知服務資源相關聯的受控識別。 只有在使用 CMK 的定價層建立資源之後，才能使用受控識別。

若要瞭解如何使用客戶管理的金鑰搭配 Azure Key Vault 來認知服務加密，請參閱：

- [使用 Key Vault 從 Azure 入口網站設定認知服務加密的客戶管理金鑰](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-portal.md)

啟用客戶管理的金鑰也會啟用系統指派的受控識別，這是一項 Azure AD 的功能。 一旦啟用系統指派的受控識別，此資源將會向 Azure Active Directory 註冊。 註冊之後，受控識別將會獲得在客戶管理的金鑰設定期間選取之 Key Vault 的存取權。 您可以深入瞭解[受控](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)識別。

> [!IMPORTANT]
> 如果您停用系統指派的受控識別，將會移除對金鑰保存庫的存取權，而且任何以客戶金鑰加密的資料將無法再存取。 所有相依于此資料的功能將會停止運作。

> [!IMPORTANT]
> 受控識別目前不支援跨目錄案例。 當您在 Azure 入口網站中設定客戶管理的金鑰時，系統會在幕後自動指派受控識別。 如果您之後將訂用帳戶、資源群組或資源從一個 Azure AD 目錄移到另一個，則與該資源相關聯的受控識別不會傳輸至新的租使用者，因此客戶管理的金鑰可能無法再使用。 如需詳細資訊，請參閱常見問題中的 Azure AD 目錄[和 Azure 資源的受控識別的已知問題](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)中**的訂**用帳戶。  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>將客戶管理的金鑰儲存在 Azure Key Vault

若要啟用客戶管理的金鑰，您必須使用 Azure Key Vault 來儲存金鑰。 您必須同時啟用「虛**刪除**」和「不要**清除**」金鑰保存庫的屬性。

認知服務加密僅支援大小為2048的 RSA 金鑰。 如需金鑰的詳細資訊，請參閱[關於 Azure Key Vault 金鑰、秘密和憑證](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)中的**Key Vault 金鑰**。

### <a name="rotate-customer-managed-keys"></a>輪替客戶管理的金鑰

您可以根據您的相容性原則，在 Azure Key Vault 中旋轉客戶管理的金鑰。 當金鑰旋轉時，您必須更新認知服務資源，以使用新的金鑰 URI。 若要瞭解如何更新資源以在 Azure 入口網站中使用新版本的金鑰，請參閱[使用 Azure 入口網站來設定認知服務的客戶管理金鑰](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-portal.md)中的 <<c0>更新金鑰版本一節。

輪替金鑰並不會觸發資源中資料的重新加密。 使用者不需要採取進一步的動作。

### <a name="revoke-access-to-customer-managed-keys"></a>撤銷對客戶管理的金鑰的存取權

若要撤銷對客戶管理的金鑰的存取權，請使用 PowerShell 或 Azure CLI。 如需詳細資訊，請參閱[Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//)或[Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault)。 撤銷存取權可有效封鎖對認知服務資源中所有資料的存取，因為認知服務無法存取加密金鑰。


