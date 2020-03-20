---
title: 待用資料的 Language Understanding 服務加密
titleSuffix: Azure Cognitive Services
description: Language Understanding 待用資料的服務加密。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 59e066974f690bda2384504cc27af5aa94b7b75b
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/14/2020
ms.locfileid: "79372434"
---
# <a name="language-understanding-service-encryption-of-data-at-rest"></a>待用資料的 Language Understanding 服務加密

當您的資料保存到雲端時，Language Understanding 服務會自動將其加密。 Language Understanding 服務加密可保護您的資料，並協助您符合組織的安全性和合規性承諾。

## <a name="about-cognitive-services-encryption"></a>關於認知服務加密

資料會使用[FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2)相容[的256位 AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)加密來加密和解密。 加密和解密都是透明的，這表示會為您管理加密和存取。 您的資料預設是安全的，而且您不需要修改程式碼或應用程式，就能利用加密。

## <a name="about-encryption-key-management"></a>關於加密金鑰管理

根據預設，您的訂用帳戶會使用 Microsoft 管理的加密金鑰。 您也可以選擇使用自己的金鑰來管理您的訂用帳戶。 客戶管理的金鑰（CMK）提供更大的彈性來建立、輪替、停用及撤銷存取控制。 您也可以審核用來保護資料的加密金鑰。

## <a name="customer-managed-keys-with-azure-key-vault"></a>客戶管理的金鑰與 Azure Key Vault

您也可以選擇使用自己的金鑰來管理您的訂用帳戶。 客戶管理的金鑰（CMK）也稱為「自備金鑰」（BYOK），可提供更大的彈性來建立、輪替、停用及撤銷存取控制。 您也可以審核用來保護資料的加密金鑰。

您必須使用 Azure Key Vault 來儲存客戶管理的金鑰。 您可以建立自己的金鑰，並將其儲存在金鑰保存庫中，或者您可以使用 Azure Key Vault Api 來產生金鑰。 認知服務資源和金鑰保存庫必須位於相同的區域和相同的 Azure Active Directory （Azure AD）租使用者中，但它們可以在不同的訂用帳戶中。 如需 Azure Key Vault 的詳細資訊，請參閱[什麼是 Azure Key Vault？](https://docs.microsoft.com/azure/key-vault/key-vault-overview)。

### <a name="customer-managed-keys-for-language-understanding"></a>Language Understanding 的客戶管理金鑰

若要要求能夠使用客戶管理的金鑰，請填寫並提交 [LUIS 服務客戶管理的金鑰要求表單](https://aka.ms/cogsvc-cmk)。 大約需要3-5 個工作天的時間，才會收到要求的狀態。 視需求而定，您可能會放在佇列中，並已核准為可用空間。 一旦核准搭配使用 CMK 與 LUIS，您將需要從 Azure 入口網站建立新的 Language Understanding 資源，然後選取 [E0] 作為定價層。 新的 SKU 的運作方式會與 F0 SKU 相同，但 CMK 除外。 使用者將無法從 F0 升級至新的 E0 SKU。

E0 資源僅適用于撰寫服務，而 E0 層一開始只會在美國西部區域提供支援。

![LUIS 訂用帳戶影像](../media/cognitive-services-encryption/luis-subscription.png)

### <a name="regional-availability"></a>區域可用性

客戶管理的金鑰目前適用于**美國西部**區域。

### <a name="limitations"></a>限制

搭配現有/先前建立的應用程式使用 E0 層時，有一些限制：

* 將會封鎖對 E0 資源的遷移。 使用者只能夠將其應用程式遷移至 F0 資源。 將現有的資源遷移至 F0 之後，您可以在 E0 層中建立新的資源。 [在這裡](https://docs.microsoft.com/azure/cognitive-services/luis/luis-migration-authoring)深入瞭解遷移。  
* 將應用程式移入或移出 E0 資源將會遭到封鎖。 這項限制的解決方法是匯出現有的應用程式，並將它匯入為 E0 資源。
* 不支援 Bing 拼寫檢查功能。
* 如果您的應用程式是 E0，則會停用記錄終端使用者流量。
* 在 E0 層中，應用程式不支援來自 Azure Bot 服務的語音預備功能。 這項功能可透過 Azure Bot 服務取得，這不支援 CMK。
* 入口網站的語音預備功能需要 Azure Blob 儲存體。 如需詳細資訊，請參閱[整合您自己的儲存體](../Speech-Service/speech-encryption-of-data-at-rest.md#bring-your-own-storage-byos-for-customization-and-logging)。

### <a name="enable-customer-managed-keys"></a>啟用客戶管理的金鑰

新的認知服務資源一律會使用 Microsoft 管理的金鑰進行加密。 建立資源時，不可能啟用客戶管理的金鑰。 客戶管理的金鑰會儲存在 Azure Key Vault 中，而金鑰保存庫必須布建存取原則，以將金鑰許可權授與與認知服務資源相關聯的受控識別。 只有在使用 CMK 的定價層建立資源之後，才能使用受控識別。

若要瞭解如何使用客戶管理的金鑰搭配 Azure Key Vault 來認知服務加密，請參閱：

- [使用 Key Vault 從 Azure 入口網站設定認知服務加密的客戶管理金鑰](../Encryption/cognitive-services-encryption-keys-portal.md)

啟用客戶管理的金鑰也會啟用系統指派的受控識別，這是一項 Azure AD 的功能。 一旦啟用系統指派的受控識別，此資源將會向 Azure Active Directory 註冊。 註冊之後，受控識別將會獲得在客戶管理的金鑰設定期間選取之 Key Vault 的存取權。 您可以深入瞭解[受控](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)識別。

> [!IMPORTANT]
> 如果您停用系統指派的受控識別，將會移除對金鑰保存庫的存取權，而且任何以客戶金鑰加密的資料將無法再存取。 所有相依于此資料的功能將會停止運作。

> [!IMPORTANT]
> 受控識別目前不支援跨目錄案例。 當您在 Azure 入口網站中設定客戶管理的金鑰時，系統會在幕後自動指派受控識別。 如果您之後將訂用帳戶、資源群組或資源從一個 Azure AD 目錄移到另一個，則與該資源相關聯的受控識別不會傳輸至新的租使用者，因此客戶管理的金鑰可能無法再使用。 如需詳細資訊，請參閱常見問題中的 Azure AD 目錄[和 Azure 資源的受控識別的已知問題](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)中**的訂**用帳戶。  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>將客戶管理的金鑰儲存在 Azure Key Vault

若要啟用客戶管理的金鑰，您必須使用 Azure Key Vault 來儲存金鑰。 您必須同時啟用「虛**刪除**」和「不要**清除**」金鑰保存庫的屬性。

認知服務加密僅支援大小為2048的 RSA 金鑰。 如需金鑰的詳細資訊，請參閱[關於 Azure Key Vault 金鑰、秘密和憑證](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)中的**Key Vault 金鑰**。

### <a name="rotate-customer-managed-keys"></a>輪替客戶管理的金鑰

您可以根據您的相容性原則，在 Azure Key Vault 中旋轉客戶管理的金鑰。 當金鑰旋轉時，您必須更新認知服務資源，以使用新的金鑰 URI。 若要瞭解如何更新資源以在 Azure 入口網站中使用新版本的金鑰，請參閱[使用 Azure 入口網站來設定認知服務的客戶管理金鑰](../Encryption/cognitive-services-encryption-keys-portal.md)中的 <<c0>更新金鑰版本一節。

輪替金鑰並不會觸發資源中資料的重新加密。 使用者不需要採取進一步的動作。

### <a name="revoke-access-to-customer-managed-keys"></a>撤銷對客戶管理的金鑰的存取權

若要撤銷對客戶管理的金鑰的存取權，請使用 PowerShell 或 Azure CLI。 如需詳細資訊，請參閱[Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//)或[Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault)。 撤銷存取權可有效封鎖對認知服務資源中所有資料的存取，因為認知服務無法存取加密金鑰。

## <a name="next-steps"></a>後續步驟

* [LUIS 服務客戶管理的金鑰要求表單](https://aka.ms/cogsvc-cmk)
* [深入瞭解 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
