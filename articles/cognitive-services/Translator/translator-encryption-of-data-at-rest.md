---
title: 待用資料的 Translator 加密
titleSuffix: Azure Cognitive Services
description: Microsoft 可讓您使用自己的金鑰（稱為客戶管理的金鑰）來管理認知服務訂用帳戶， (CMK) 。 本文涵蓋 Translator 的靜止資料加密，以及如何啟用和管理 CMK。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: ce7ff6ae134835de23a0d2670e8b4f44783654f8
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89079195"
---
# <a name="translator-encryption-of-data-at-rest"></a>待用資料的 Translator 加密

Translator 會自動將您上傳的資料加密，以建立自訂翻譯模型（當它保存到雲端時），以協助滿足您組織的安全性和合規性目標。

## <a name="about-cognitive-services-encryption"></a>關於認知服務加密

資料會使用 [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) 相容 [的256位 AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 加密進行加密和解密。 加密和解密是透明的，這表示會為您管理加密和存取。 根據預設，您的資料會受到保護，因此您無須修改程式碼或應用程式來利用加密功能。

## <a name="about-encryption-key-management"></a>關於加密金鑰管理

根據預設，您的訂用帳戶會使用由 Microsoft 管理的加密金鑰。 如果您使用的定價層支援客戶管理的金鑰，您可以在[Azure 入口網站](https://portal.azure.com)的 [**加密**] 區段中看到資源的加密設定，如下圖所示。

![查看加密設定](../media/cognitive-services-encryption/encryptionblade.png)

針對僅支援 Microsoft 管理之加密金鑰的訂用帳戶，您將不會有 **加密** 區段。

## <a name="customer-managed-keys-with-azure-key-vault"></a>客戶管理的金鑰與 Azure Key Vault

根據預設，您的訂用帳戶會使用由 Microsoft 管理的加密金鑰。 您也可以選擇使用您自己的金鑰來管理訂用帳戶，稱為客戶管理的金鑰 (CMK) 。 CMK 提供更大的彈性來建立、輪替、停用及撤銷存取控制。 您也可稽核用來保護資料的加密金鑰。 如果已針對您的訂用帳戶設定 CMK，則會提供雙重加密，以提供第二層保護，並可讓您透過 Azure Key Vault 控制加密金鑰。

> [!IMPORTANT]
> 客戶管理的金鑰適用于 Translator 服務的所有定價層。 若要要求能夠使用客戶管理的金鑰，請填寫並提交 [Translator 客戶管理的金鑰要求表單](https://aka.ms/cogsvc-cmk) ，大約需要3-5 個工作天的時間，才會收到要求的狀態。 視需求而定，您可能會被放入佇列中，並在可用的空間獲得核准。 核准使用 CMK 搭配 Translator 服務之後，您將需要建立新的翻譯工具資源。 建立 Translator 資源之後，您可以使用 Azure Key Vault 來設定受控識別。

請遵循下列步驟，為 Translator 啟用客戶管理的金鑰：

1. 建立新的區域翻譯或區域認知服務資源。 這不會與全域資源搭配使用。
2. 在 Azure 入口網站中啟用受控識別，並新增客戶管理的金鑰資訊。
3. 在自訂翻譯中建立新的工作區，並將此訂用帳戶資訊相關聯。

### <a name="enable-customer-managed-keys"></a>啟用客戶管理的金鑰

您必須使用 Azure Key Vault 儲存客戶管理的金鑰。 您可以建立自己的金鑰並將其儲存在金鑰保存庫中，或是使用 Azure Key Vault API 來產生金鑰。 認知服務資源和金鑰保存庫必須位於相同的區域中，且在相同的 Azure Active Directory (Azure AD) 租使用者中，但它們可以在不同的訂用帳戶中。 如需 Azure Key Vault 的詳細資訊，請參閱 [什麼是 Azure Key Vault？](https://docs.microsoft.com/azure/key-vault/key-vault-overview)。

新的認知服務資源一律會使用 Microsoft 管理的金鑰進行加密。 建立資源時，無法啟用客戶管理的金鑰。 客戶管理的金鑰會儲存在 Azure Key Vault 中，而金鑰保存庫必須布建存取原則，以將金鑰許可權授與與認知服務資源相關聯的受控識別。 一旦建立資源之後，就可以使用受控識別。

若要瞭解如何搭配 Azure Key Vault 使用客戶管理的金鑰進行認知服務加密，請參閱：

- [使用 Azure 入口網站的認知服務加密 Key Vault 設定客戶管理的金鑰](../Encryption/cognitive-services-encryption-keys-portal.md)

啟用客戶管理的金鑰也會啟用系統指派的受控識別，這是 Azure AD 的功能。 一旦啟用系統指派的受控識別，此資源就會向 Azure Active Directory 註冊。 註冊之後，受控識別將會獲得在客戶管理的金鑰設定期間選取的 Key Vault 存取權。 您可以深入瞭解 [受控](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)識別。

> [!IMPORTANT]
> 如果您停用系統指派的受控識別，將會移除對金鑰保存庫的存取權，而且將無法再存取使用客戶金鑰加密的任何資料。 相依于此資料的任何功能將會停止運作。 任何您已部署的模型也將會解除部署。 所有上傳的資料都會從自訂翻譯刪除。 如果已重新啟用受控識別，我們將不會自動為您重新部署模型。

> [!IMPORTANT]
> 受控識別目前不支援跨目錄案例。 當您在 Azure 入口網站中設定客戶管理的金鑰時，系統會自動在幕後指派受控識別。 如果您之後將訂用帳戶、資源群組或資源從某個 Azure AD 目錄移至另一個目錄，與該資源相關聯的受控識別不會傳送至新的租使用者，因此客戶管理的金鑰可能無法再運作。 如需詳細資訊，請參閱在[Azure 資源受控識別的常見問題和已知問題](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)中，**傳輸 Azure AD 目錄之間的訂**用帳戶。  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>在 Azure Key Vault 中儲存客戶管理的金鑰

若要啟用客戶管理的金鑰，您必須使用 Azure Key Vault 來儲存您的金鑰。 您必須同時啟用虛 **刪除** 和不 **清除** 金鑰保存庫的屬性。

認知服務加密只支援大小為2048的 RSA 金鑰。 如需有關金鑰的詳細資訊，請參閱[關於 Azure Key Vault 金鑰、秘密和憑證](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)的**Key Vault 金鑰**。

> [!NOTE]
> 如果刪除整個金鑰保存庫，將不會再顯示您的資料，而且您的所有模型都將解除部署。 所有上傳的資料都會從自訂翻譯刪除。 

### <a name="revoke-access-to-customer-managed-keys"></a>撤銷客戶管理金鑰的存取權

若要撤銷客戶管理金鑰的存取權，請使用 PowerShell 或 Azure CLI。 如需詳細資訊，請參閱 [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) 或 [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault)。 撤銷存取權實際上會封鎖對認知服務資源中所有資料的存取，而您的模型將會解除部署，因為認知服務無法存取加密金鑰。 也會從自訂翻譯中刪除所有上傳的資料。


## <a name="next-steps"></a>後續步驟

* [深入瞭解 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
