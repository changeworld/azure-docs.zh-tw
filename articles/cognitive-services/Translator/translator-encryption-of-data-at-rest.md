---
title: 靜態資料的轉換器加密
titleSuffix: Azure Cognitive Services
description: 靜態資料的轉換器加密。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 44bb223dbc944be1b8769aa2572f1b88b916528b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372416"
---
# <a name="translator-encryption-of-data-at-rest"></a>靜態資料的轉換器加密

當資料保存到雲中時，轉換器會自動加密資料，當您上傳這些資料以構建自訂翻譯模型時，有助於實現組織安全性和合規性目標。

## <a name="about-cognitive-services-encryption"></a>關於認知服務加密

使用[FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2)相容[的 256 位 AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)加密對資料進行加密和解密。 加密和解密是透明的，這意味著加密和訪問是為您管理的。 預設情況下，您的資料是安全的，您無需修改代碼或應用程式就可以利用加密。

## <a name="about-encryption-key-management"></a>關於加密金鑰管理

預設情況下，您的訂閱使用 Microsoft 管理的加密金鑰。 如果使用的是支援客戶管理的金鑰的定價層，則可以在[Azure 門戶](https://portal.azure.com)的 **"加密"** 部分中查看資源的加密設定，如下圖所示。

![查看加密設定](../media/cognitive-services-encryption/encryptionblade.png)

對於僅支援 Microsoft 管理的加密金鑰的訂閱，您將沒有**加密**部分。

## <a name="customer-managed-keys-with-azure-key-vault"></a>使用 Azure 金鑰保存庫的客戶管理金鑰

還有一個選項可以用自己的金鑰管理訂閱。 客戶管理的金鑰 （CMK）也稱為"自帶金鑰 （BYOK"），為創建、旋轉、禁用和撤銷存取控制提供了更大的靈活性。 您還可以審核用於保護資料的加密金鑰。

> [!IMPORTANT]
> 客戶管理的金鑰可用於翻譯服務的所有定價層。 要請求使用客戶管理的金鑰，請填寫並提交[翻譯客戶管理金鑰請求表](https://aka.ms/cogsvc-cmk)，大約需要 3-5 個工作日才能回聽您的請求狀態。 根據需求，您可能會被放置在佇列中，並在可用空間時獲得批准。 一旦批准將 CMK 與翻譯服務一起使用，您將需要創建新的翻譯資源。 創建翻譯資源後，可以使用 Azure 金鑰保存庫設置託管標識。

按照以下步驟為譯員啟用客戶管理的金鑰：

1. 創建新的區域翻譯文本或區域認知服務資源。 這不適用於全域資源。
2. 在 Azure 門戶中啟用託管標識，並添加客戶管理的關鍵資訊。
3. 在自訂轉換器中創建新工作區並關聯此訂閱資訊。

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

### <a name="enable-customer-managed-keys"></a>啟用客戶管理的金鑰

您必須使用 Azure 金鑰保存庫來存儲客戶管理的金鑰。 您可以創建自己的金鑰並將其存儲在金鑰保存庫中，也可以使用 Azure 金鑰保存庫 API 生成金鑰。 認知服務資源和金鑰保存庫必須位於同一區域和同一 Azure 活動目錄 （Azure AD） 租戶中，但它們可以位於不同的訂閱中。 有關 Azure 金鑰保存庫的詳細資訊，請參閱[什麼是 Azure 金鑰保存庫？](https://docs.microsoft.com/azure/key-vault/key-vault-overview)

新的認知服務資源始終使用 Microsoft 管理的金鑰進行加密。 在創建資源時無法啟用客戶管理的金鑰。 客戶管理的金鑰存儲在 Azure 金鑰保存庫中，並且必須預配金鑰保存庫，這些訪問策略向與認知服務資源關聯的託管標識授予金鑰許可權。 創建資源後，託管標識即可用。

要瞭解如何將客戶管理的金鑰與 Azure 金鑰保存庫用於認知服務加密，請參閱：

- [使用金鑰保存庫配置客戶管理的金鑰，以便從 Azure 門戶進行認知服務加密](../Encryption/cognitive-services-encryption-keys-portal.md)

啟用客戶託管金鑰還將啟用系統分配的託管標識，這是 Azure AD 的一項功能。 啟用系統分配的託管標識後，此資源將註冊到 Azure 活動目錄。 註冊後，託管標識將有權訪問客戶託管金鑰設置期間選擇的金鑰保存庫。 您可以瞭解有關[託管標識的更多資訊](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。

> [!IMPORTANT]
> 如果禁用系統分配的託管標識，將刪除對金鑰保存庫的訪問，並且將不再訪問使用客戶金鑰加密的任何資料。 依賴于此資料的任何功能都將停止工作。 您部署的任何模型也將取消部署。 所有上傳的資料將從自訂轉換器中刪除。 如果託管標識已重新啟用，我們不會自動為您重新部署模型。

> [!IMPORTANT]
> 受控識別目前不支援跨目錄案例。 在 Azure 門戶中配置客戶託管金鑰時，託管標識將自動在封面下分配。 如果隨後將訂閱、資源組或資源從一個 Azure AD 目錄移動到另一個 Azure AD 目錄，則與資源關聯的託管標識不會傳輸到新租戶，因此客戶託管金鑰可能不再工作。 有關詳細資訊，請參閱在 FAQ 中的 Azure AD 目錄[和 Azure 資源的託管標識的已知問題](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)**之間傳輸訂閱**。  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>在 Azure 金鑰保存庫中存儲客戶管理的金鑰

要啟用客戶管理的金鑰，必須使用 Azure 金鑰保存庫來存儲金鑰。 您必須在金鑰保存庫中同時啟用 **"虛刪除****"和"不清除"** 屬性。

認知服務加密僅支援大小為 2048 的 RSA 金鑰。 有關金鑰的詳細資訊，請參閱["關於 Azure 金鑰保存庫金鑰、機密和證書"](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)中的**金鑰保存庫金鑰**。

> [!NOTE]
> 如果刪除了整個金鑰保存庫，您的資料將不再顯示，並且所有模型都將取消部署。 所有上傳的資料將從自訂轉換器中刪除。 

### <a name="revoke-access-to-customer-managed-keys"></a>撤銷對客戶管理金鑰的訪問

要撤銷對客戶管理金鑰的存取權限，請使用 PowerShell 或 Azure CLI。 有關詳細資訊，請參閱[Azure 金鑰保存庫 PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//)或[Azure 金鑰保存庫 CLI](https://docs.microsoft.com/cli/azure/keyvault)。 撤銷訪問可有效地阻止對認知服務資源和模型中的所有資料的訪問，因為認知服務無法訪問加密金鑰。 所有上傳的資料也將從自訂轉換器中刪除。


## <a name="next-steps"></a>後續步驟

* [瞭解有關 Azure 金鑰保存庫的更多](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
