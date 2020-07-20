---
title: 待用資料的 Translator 加密
titleSuffix: Azure Cognitive Services
description: 待用資料的 Translator encryption。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: egeaney
ms.openlocfilehash: bc328efd648eb3dd522f5233e2a5c440911ac58c
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310830"
---
# <a name="translator-encryption-of-data-at-rest"></a>待用資料的 Translator 加密

Translator 會自動加密您的資料，您可以上傳來建立自訂翻譯模型（當它保存到雲端時），以協助符合您的組織安全性和合規性目標。

## <a name="about-cognitive-services-encryption"></a>關於認知服務加密

資料會使用[FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2)相容[的256位 AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)加密來加密和解密。 加密和解密都是透明的，這表示會為您管理加密和存取。 您的資料預設是安全的，而且您不需要修改程式碼或應用程式，就能利用加密。

## <a name="about-encryption-key-management"></a>關於加密金鑰管理

根據預設，您的訂用帳戶會使用 Microsoft 管理的加密金鑰。 如果您使用的定價層支援客戶管理的金鑰，您可以在[Azure 入口網站](https://portal.azure.com)的 [**加密**] 區段中看到資源的加密設定，如下圖所示。

![查看加密設定](../media/cognitive-services-encryption/encryptionblade.png)

對於僅支援受 Microsoft 管理之加密金鑰的訂用帳戶，您將不會有**加密**區段。

## <a name="customer-managed-keys-with-azure-key-vault"></a>客戶管理的金鑰與 Azure Key Vault

您也可以選擇使用自己的金鑰來管理您的訂用帳戶。 客戶管理的金鑰（CMK）也稱為「自備金鑰」（BYOK），可提供更大的彈性來建立、輪替、停用及撤銷存取控制。 您也可以審核用來保護資料的加密金鑰。

> [!IMPORTANT]
> 客戶管理的金鑰適用于 Translator 服務的所有定價層。 若要要求能夠使用客戶管理的金鑰，請填寫並提交[Translator 客戶管理的金鑰要求表單](https://aka.ms/cogsvc-cmk)，大約需要3-5 個工作天的時間，才會收到要求的狀態。 視需求而定，您可能會放在佇列中，並已核准為可用空間。 一旦核准搭配 Translator 服務使用 CMK，您就必須建立新的 Translator 資源。 建立您的 Translator 資源之後，您可以使用 Azure Key Vault 來設定受控識別。

請遵循下列步驟，為 Translator 啟用客戶管理的金鑰：

1. 建立新的地區翻譯工具或區域認知服務資源。 這不會與全域資源搭配使用。
2. 已在 Azure 入口網站中啟用受控識別，並新增客戶管理的金鑰資訊。
3. 在自訂翻譯中建立新的工作區，並建立此訂用帳戶資訊的關聯。

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

### <a name="enable-customer-managed-keys"></a>啟用客戶管理的金鑰

您必須使用 Azure Key Vault 來儲存客戶管理的金鑰。 您可以建立自己的金鑰並將其儲存在金鑰保存庫中，或是使用 Azure Key Vault API 來產生金鑰。 認知服務資源和金鑰保存庫必須位於相同的區域和相同的 Azure Active Directory （Azure AD）租使用者中，但它們可以在不同的訂用帳戶中。 如需 Azure Key Vault 的詳細資訊，請參閱[什麼是 Azure Key Vault？](https://docs.microsoft.com/azure/key-vault/key-vault-overview)。

新的認知服務資源一律會使用 Microsoft 管理的金鑰進行加密。 建立資源時，不可能啟用客戶管理的金鑰。 客戶管理的金鑰會儲存在 Azure Key Vault 中，而金鑰保存庫必須布建存取原則，以將金鑰許可權授與與認知服務資源相關聯的受控識別。 一旦建立資源，就可以使用受控識別。

若要瞭解如何使用客戶管理的金鑰搭配 Azure Key Vault 來認知服務加密，請參閱：

- [使用 Key Vault 從 Azure 入口網站設定認知服務加密的客戶管理金鑰](../Encryption/cognitive-services-encryption-keys-portal.md)

啟用客戶管理的金鑰也會啟用系統指派的受控識別，這是一項 Azure AD 的功能。 一旦啟用系統指派的受控識別，此資源將會向 Azure Active Directory 註冊。 註冊之後，受控識別將會獲得在客戶管理的金鑰設定期間選取之 Key Vault 的存取權。 您可以深入瞭解[受控](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)識別。

> [!IMPORTANT]
> 如果您停用系統指派的受控識別，將會移除對金鑰保存庫的存取權，而且任何以客戶金鑰加密的資料將無法再存取。 所有相依于此資料的功能將會停止運作。 您已部署的任何模型也會進行解除部署。 所有上傳的資料都會從自訂翻譯刪除。 如果受控識別已重新啟用，我們不會自動為您重新部署模型。

> [!IMPORTANT]
> 受控識別目前不支援跨目錄案例。 當您在 Azure 入口網站中設定客戶管理的金鑰時，系統會在幕後自動指派受控識別。 如果您之後將訂用帳戶、資源群組或資源從一個 Azure AD 目錄移到另一個，則與該資源相關聯的受控識別不會傳輸至新的租使用者，因此客戶管理的金鑰可能無法再使用。 如需詳細資訊，請參閱常見問題中的 Azure AD 目錄[和 Azure 資源的受控識別的已知問題](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)中**的訂**用帳戶。  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>將客戶管理的金鑰儲存在 Azure Key Vault

若要啟用客戶管理的金鑰，您必須使用 Azure Key Vault 來儲存金鑰。 您必須同時啟用「虛**刪除**」和「不要**清除**」金鑰保存庫的屬性。

認知服務加密僅支援大小為2048的 RSA 金鑰。 如需金鑰的詳細資訊，請參閱[關於 Azure Key Vault 金鑰、秘密和憑證](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)中的**Key Vault 金鑰**。

> [!NOTE]
> 如果整個金鑰保存庫已刪除，將不會再顯示您的資料，而且您的所有模型都會解除部署。 所有上傳的資料都會從自訂翻譯刪除。 

### <a name="revoke-access-to-customer-managed-keys"></a>撤銷對客戶管理的金鑰的存取權

若要撤銷對客戶管理的金鑰的存取權，請使用 PowerShell 或 Azure CLI。 如需詳細資訊，請參閱[Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//)或[Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault)。 撤銷存取權可有效封鎖對認知服務資源中所有資料的存取，而且將會解除部署您的模型，因為認知服務無法存取加密金鑰。 所有上傳的資料也會從自訂翻譯中刪除。


## <a name="next-steps"></a>後續步驟

* [深入瞭解 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
