---
title: 靜態資料的語言理解服務加密
titleSuffix: Azure Cognitive Services
description: 語言理解服務對靜態資料進行加密。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 59e066974f690bda2384504cc27af5aa94b7b75b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372434"
---
# <a name="language-understanding-service-encryption-of-data-at-rest"></a>靜態資料的語言理解服務加密

語言理解服務在資料保存到雲時會自動加密資料。 語言理解服務加密可保護您的資料，並説明您履行組織安全和合規性承諾。

## <a name="about-cognitive-services-encryption"></a>關於認知服務加密

使用[FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2)相容[的 256 位 AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)加密對資料進行加密和解密。 加密和解密是透明的，這意味著加密和訪問是為您管理的。 預設情況下，您的資料是安全的，您無需修改代碼或應用程式就可以利用加密。

## <a name="about-encryption-key-management"></a>關於加密金鑰管理

預設情況下，您的訂閱使用 Microsoft 管理的加密金鑰。 還有一個選項可以用自己的金鑰管理訂閱。 客戶管理的金鑰 （CMK） 提供了創建、旋轉、禁用和撤銷存取控制的更大靈活性。 您還可以審核用於保護資料的加密金鑰。

## <a name="customer-managed-keys-with-azure-key-vault"></a>使用 Azure 金鑰保存庫的客戶管理金鑰

還有一個選項可以用自己的金鑰管理訂閱。 客戶管理的金鑰 （CMK）也稱為"自帶金鑰 （BYOK"），為創建、旋轉、禁用和撤銷存取控制提供了更大的靈活性。 您還可以審核用於保護資料的加密金鑰。

您必須使用 Azure 金鑰保存庫來存儲客戶管理的金鑰。 您可以創建自己的金鑰並將其存儲在金鑰保存庫中，也可以使用 Azure 金鑰保存庫 API 生成金鑰。 認知服務資源和金鑰保存庫必須位於同一區域和同一 Azure 活動目錄 （Azure AD） 租戶中，但它們可以位於不同的訂閱中。 有關 Azure 金鑰保存庫的詳細資訊，請參閱[什麼是 Azure 金鑰保存庫？](https://docs.microsoft.com/azure/key-vault/key-vault-overview)

### <a name="customer-managed-keys-for-language-understanding"></a>客戶管理的語言理解金鑰

要請求使用客戶管理的金鑰，請填寫並提交 [LUIS 服務客戶管理金鑰請求表](https://aka.ms/cogsvc-cmk)。 大約需要 3-5 個工作日才能回復您的請求狀態。 根據需求，您可能會被放置在佇列中，並在可用空間時獲得批准。 批准將 CMK 與 LUIS 一起使用後，您需要從 Azure 門戶創建新的語言理解資源，並選擇 E0 作為定價層。 除 CMK 外，新的 SKU 的功能與已可用的 F0 SKU 相同。 使用者將無法從 F0 升級到新的 E0 SKU。

E0 資源僅適用于創作服務，E0 層最初僅在美國西部區域受支援。

![LUIS 訂閱映射](../media/cognitive-services-encryption/luis-subscription.png)

### <a name="regional-availability"></a>區域可用性

客戶管理的金鑰目前**在美國西部**區域可用。

### <a name="limitations"></a>限制

將 E0 層與現有/以前創建的應用程式一起使用時存在一些限制：

* 將阻止遷移到 E0 資源。 使用者只能將其應用遷移到 F0 資源。 將現有資源遷移到 F0 後，可以在 E0 層中創建新資源。 [在此處瞭解有關遷移的更多詳細資訊](https://docs.microsoft.com/azure/cognitive-services/luis/luis-migration-authoring)。  
* 將阻止將應用程式移動到或從 E0 資源移動到或從 E0 資源移動到。 此限制的解決方法是匯出現有應用程式，並將其導入為 E0 資源。
* 不支援必應拼寫檢查功能。
* 如果應用程式為 E0，則禁用日誌記錄最終使用者流量。
* E0 層中的應用程式不支援 Azure Bot 服務中的語音準備功能。 此功能可通過不支援 CMK 的 Azure 機器人服務獲得。
* 來自門戶的語音啟動功能需要 Azure Blob 存儲。 有關詳細資訊，請參閱[自帶存儲](../Speech-Service/speech-encryption-of-data-at-rest.md#bring-your-own-storage-byos-for-customization-and-logging)。

### <a name="enable-customer-managed-keys"></a>啟用客戶管理的金鑰

新的認知服務資源始終使用 Microsoft 管理的金鑰進行加密。 在創建資源時無法啟用客戶管理的金鑰。 客戶管理的金鑰存儲在 Azure 金鑰保存庫中，並且必須預配金鑰保存庫，這些訪問策略向與認知服務資源關聯的託管標識授予金鑰許可權。 只有在使用 CMK 的定價層創建資源後，託管標識才可用。

要瞭解如何將客戶管理的金鑰與 Azure 金鑰保存庫用於認知服務加密，請參閱：

- [使用金鑰保存庫配置客戶管理的金鑰，以便從 Azure 門戶進行認知服務加密](../Encryption/cognitive-services-encryption-keys-portal.md)

啟用客戶託管金鑰還將啟用系統分配的託管標識，這是 Azure AD 的一項功能。 啟用系統分配的託管標識後，此資源將註冊到 Azure 活動目錄。 註冊後，託管標識將有權訪問客戶託管金鑰設置期間選擇的金鑰保存庫。 您可以瞭解有關[託管標識的更多資訊](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。

> [!IMPORTANT]
> 如果禁用系統分配的託管標識，將刪除對金鑰保存庫的訪問，並且將不再訪問使用客戶金鑰加密的任何資料。 依賴于此資料的任何功能都將停止工作。

> [!IMPORTANT]
> 受控識別目前不支援跨目錄案例。 在 Azure 門戶中配置客戶託管金鑰時，託管標識將自動在封面下分配。 如果隨後將訂閱、資源組或資源從一個 Azure AD 目錄移動到另一個 Azure AD 目錄，則與資源關聯的託管標識不會傳輸到新租戶，因此客戶託管金鑰可能不再工作。 有關詳細資訊，請參閱在 FAQ 中的 Azure AD 目錄[和 Azure 資源的託管標識的已知問題](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)**之間傳輸訂閱**。  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>在 Azure 金鑰保存庫中存儲客戶管理的金鑰

要啟用客戶管理的金鑰，必須使用 Azure 金鑰保存庫來存儲金鑰。 您必須在金鑰保存庫中同時啟用 **"虛刪除****"和"不清除"** 屬性。

認知服務加密僅支援大小為 2048 的 RSA 金鑰。 有關金鑰的詳細資訊，請參閱["關於 Azure 金鑰保存庫金鑰、機密和證書"](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)中的**金鑰保存庫金鑰**。

### <a name="rotate-customer-managed-keys"></a>旋轉客戶管理的金鑰

您可以根據合規性策略在 Azure 金鑰保存庫中輪換客戶管理的金鑰。 旋轉金鑰時，必須更新認知服務資源才能使用新的金鑰 URI。 要瞭解如何更新資源以在 Azure 門戶中使用金鑰的新版本，請參閱[使用 Azure 門戶在"配置認知服務的客戶管理金鑰"](../Encryption/cognitive-services-encryption-keys-portal.md)中**更新金鑰版本的**部分。

旋轉金鑰不會觸發資源中資料的重新加密。 使用者無需執行進一步操作。

### <a name="revoke-access-to-customer-managed-keys"></a>撤銷對客戶管理金鑰的訪問

要撤銷對客戶管理金鑰的存取權限，請使用 PowerShell 或 Azure CLI。 有關詳細資訊，請參閱[Azure 金鑰保存庫 PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//)或[Azure 金鑰保存庫 CLI](https://docs.microsoft.com/cli/azure/keyvault)。 撤銷訪問可有效地阻止對認知服務資源中的所有資料的訪問，因為認知服務無法訪問加密金鑰。

## <a name="next-steps"></a>後續步驟

* [LUIS 服務客戶管理的關鍵請求表](https://aka.ms/cogsvc-cmk)
* [瞭解有關 Azure 金鑰保存庫的更多](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
