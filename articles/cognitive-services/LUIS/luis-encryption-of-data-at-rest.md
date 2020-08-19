---
title: 靜態資料的 Language Understanding 服務加密
titleSuffix: Azure Cognitive Services
description: 靜態資料的 Language Understanding 服務加密。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 4fc816c3894120a5d1b356d91ebebbc56f21b530
ms.sourcegitcommit: ff19f4ecaff33a414c0fa2d4c92542d6e91332f8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "85052700"
---
# <a name="language-understanding-service-encryption-of-data-at-rest"></a>靜態資料的 Language Understanding 服務加密

當您將資料保存到雲端時，Language Understanding 服務會自動將您的資料加密。 Language Understanding 服務加密可保護您的資料，並協助您符合組織的安全性和合規性承諾。

## <a name="about-cognitive-services-encryption"></a>關於認知服務加密

資料會使用 [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) 相容 [的256位 AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 加密進行加密和解密。 加密和解密是透明的，這表示會為您管理加密和存取。 根據預設，您的資料會受到保護，因此您無須修改程式碼或應用程式來利用加密功能。

## <a name="about-encryption-key-management"></a>關於加密金鑰管理

根據預設，您的訂用帳戶會使用由 Microsoft 管理的加密金鑰。 您也可以選擇使用自己的金鑰來管理您的訂用帳戶。 客戶管理的金鑰 (CMK) ，可提供更大的彈性來建立、輪替、停用及撤銷存取控制。 您也可稽核用來保護資料的加密金鑰。

## <a name="customer-managed-keys-with-azure-key-vault"></a>客戶管理的金鑰與 Azure Key Vault

您也可以選擇使用自己的金鑰來管理您的訂用帳戶。 客戶管理的金鑰 (CMK) ，也稱為自備金鑰 (BYOK) ，可提供更大的彈性來建立、輪替、停用及撤銷存取控制。 您也可稽核用來保護資料的加密金鑰。

您必須使用 Azure Key Vault 儲存客戶管理的金鑰。 您可以建立自己的金鑰並將其儲存在金鑰保存庫中，或是使用 Azure Key Vault API 來產生金鑰。 認知服務資源和金鑰保存庫必須位於相同的區域中，且在相同的 Azure Active Directory (Azure AD) 租使用者中，但它們可以在不同的訂用帳戶中。 如需 Azure Key Vault 的詳細資訊，請參閱 [什麼是 Azure Key Vault？](https://docs.microsoft.com/azure/key-vault/key-vault-overview)。

### <a name="customer-managed-keys-for-language-understanding"></a>Language Understanding 客戶管理的金鑰

若要要求能夠使用客戶管理的金鑰，請填寫並提交 [LUIS Service 客戶管理的金鑰要求表單](https://aka.ms/cogsvc-cmk)。 大約需要3-5 個工作天的時間，才會收到要求的狀態。 視需求而定，您可能會被放入佇列中，並在可用的空間獲得核准。 核准使用 CMK 搭配 LUIS 之後，您必須從 Azure 入口網站建立新的 Language Understanding 資源，並選取 [E0] 作為定價層。 新的 SKU 運作方式與 F0 SKU 相同，除了 CMK 以外，已可供使用。 使用者將無法從 F0 升級至新的 E0 SKU。

![LUIS 訂用帳戶影像](../media/cognitive-services-encryption/luis-subscription.png)

### <a name="regional-availability"></a>區域可用性

客戶管理的金鑰可在所有 [撰寫區域](luis-reference-regions.md)中使用。 

### <a name="limitations"></a>限制

使用 E0 層搭配現有/先前建立的應用程式時，有一些限制：

* 將會封鎖遷移至 E0 資源。 使用者只能將其應用程式遷移至 F0 資源。 將現有資源遷移至 F0 之後，您可以在 E0 層中建立新的資源。 請 [在這裡](https://docs.microsoft.com/azure/cognitive-services/luis/luis-migration-authoring)深入瞭解遷移。  
* 將應用程式移入或移出 E0 資源將會遭到封鎖。 這項限制的解決方式是匯出現有的應用程式，並將它匯入為 E0 資源。
* 不支援 Bing 拼寫檢查功能。
* 如果您的應用程式是 E0，則會停用記錄終端使用者流量。
* E0 層中的應用程式不支援來自 Azure Bot service 的語音預備功能。 這項功能可透過 Azure Bot Service 取得，但不支援 CMK。
* 入口網站中的語音預備功能需要 Azure Blob 儲存體。 如需詳細資訊，請參閱 [攜帶您自己的儲存體](../Speech-Service/speech-encryption-of-data-at-rest.md#bring-your-own-storage-byos-for-customization-and-logging)。

### <a name="enable-customer-managed-keys"></a>啟用客戶管理的金鑰

新的認知服務資源一律會使用 Microsoft 管理的金鑰進行加密。 建立資源時，無法啟用客戶管理的金鑰。 客戶管理的金鑰會儲存在 Azure Key Vault 中，而金鑰保存庫必須布建存取原則，以將金鑰許可權授與與認知服務資源相關聯的受控識別。 只有在使用 CMK 定價層建立資源之後，才可以使用受控識別。

若要瞭解如何搭配 Azure Key Vault 使用客戶管理的金鑰進行認知服務加密，請參閱：

- [使用 Azure 入口網站的認知服務加密 Key Vault 設定客戶管理的金鑰](../Encryption/cognitive-services-encryption-keys-portal.md)

啟用客戶管理的金鑰也會啟用系統指派的受控識別，這是 Azure AD 的功能。 一旦啟用系統指派的受控識別，此資源就會向 Azure Active Directory 註冊。 註冊之後，受控識別將會獲得在客戶管理的金鑰設定期間選取的 Key Vault 存取權。 您可以深入瞭解 [受控](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)識別。

> [!IMPORTANT]
> 如果您停用系統指派的受控識別，將會移除對金鑰保存庫的存取權，而且將無法再存取使用客戶金鑰加密的任何資料。 相依于此資料的任何功能將會停止運作。

> [!IMPORTANT]
> 受控識別目前不支援跨目錄案例。 當您在 Azure 入口網站中設定客戶管理的金鑰時，系統會自動在幕後指派受控識別。 如果您之後將訂用帳戶、資源群組或資源從某個 Azure AD 目錄移至另一個目錄，與該資源相關聯的受控識別不會傳送至新的租使用者，因此客戶管理的金鑰可能無法再運作。 如需詳細資訊，請參閱在[Azure 資源受控識別的常見問題和已知問題](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)中，**傳輸 Azure AD 目錄之間的訂**用帳戶。  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>在 Azure Key Vault 中儲存客戶管理的金鑰

若要啟用客戶管理的金鑰，您必須使用 Azure Key Vault 來儲存您的金鑰。 您必須同時啟用虛 **刪除** 和不 **清除** 金鑰保存庫的屬性。

認知服務加密只支援大小為2048的 RSA 金鑰。 如需有關金鑰的詳細資訊，請參閱[關於 Azure Key Vault 金鑰、秘密和憑證](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)的**Key Vault 金鑰**。

### <a name="rotate-customer-managed-keys"></a>輪替客戶管理的金鑰

您可以根據您的合規性原則，在 Azure Key Vault 中輪替客戶管理的金鑰。 輪替金鑰時，您必須更新認知服務資源，以使用新的金鑰 URI。 若要瞭解如何更新資源以在 Azure 入口網站中使用新版本的金鑰，請參閱標題為「[使用 Azure 入口網站為認知服務設定客戶管理的金鑰](../Encryption/cognitive-services-encryption-keys-portal.md)」中的「**更新金鑰版本**」一節。

輪替金鑰並不會觸發資源中資料的重新加密。 使用者不需要採取任何進一步的動作。

### <a name="revoke-access-to-customer-managed-keys"></a>撤銷客戶管理金鑰的存取權

若要撤銷客戶管理金鑰的存取權，請使用 PowerShell 或 Azure CLI。 如需詳細資訊，請參閱 [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) 或 [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault)。 撤銷存取權實際上會封鎖對認知服務資源中所有資料的存取，因為認知服務無法存取加密金鑰。

## <a name="next-steps"></a>後續步驟

* [LUIS Service 客戶管理的金鑰要求表單](https://aka.ms/cogsvc-cmk)
* [深入瞭解 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
