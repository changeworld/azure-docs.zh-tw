---
title: 包含檔案
description: 包含檔案
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.custom: include
ms.openlocfilehash: e5e1755e6351d308c041de5cefe943e9874c8ebd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372408"
---
### <a name="enable-customer-managed-keys"></a>啟用客戶管理的金鑰

您必須使用 Azure 金鑰保存庫來存儲客戶管理的金鑰。 您可以創建自己的金鑰並將其存儲在金鑰保存庫中，也可以使用 Azure 金鑰保存庫 API 生成金鑰。 認知服務資源和金鑰保存庫必須位於同一區域和同一 Azure 活動目錄 （Azure AD） 租戶中，但它們可以位於不同的訂閱中。 有關 Azure 金鑰保存庫的詳細資訊，請參閱[什麼是 Azure 金鑰保存庫？](https://docs.microsoft.com/azure/key-vault/key-vault-overview)

新的認知服務資源始終使用 Microsoft 管理的金鑰進行加密。 在創建資源時無法啟用客戶管理的金鑰。 客戶管理的金鑰存儲在 Azure 金鑰保存庫中，並且必須預配金鑰保存庫，這些訪問策略向與認知服務資源關聯的託管標識授予金鑰許可權。 只有在使用 CMK 的定價層創建資源後，託管標識才可用。

要瞭解如何將客戶管理的金鑰與 Azure 金鑰保存庫用於認知服務加密，請參閱：

- [使用金鑰保存庫配置客戶管理的金鑰，以便從 Azure 門戶進行認知服務加密](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-portal.md)

啟用客戶託管金鑰還將啟用系統分配的託管標識，這是 Azure AD 的一項功能。 啟用系統分配的託管標識後，此資源將註冊到 Azure 活動目錄。 註冊後，託管標識將有權訪問客戶託管金鑰設置期間選擇的金鑰保存庫。 您可以瞭解有關[託管標識的更多資訊](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。

> [!IMPORTANT]
> 如果禁用系統分配的託管標識，將刪除對金鑰保存庫的訪問，並且將不再訪問使用客戶金鑰加密的任何資料。 依賴于此資料的任何功能都將停止工作。

> [!IMPORTANT]
> 受控識別目前不支援跨目錄案例。 在 Azure 門戶中配置客戶託管金鑰時，託管標識將自動在封面下分配。 如果隨後將訂閱、資源組或資源從一個 Azure AD 目錄移動到另一個 Azure AD 目錄，則與資源關聯的託管標識不會傳輸到新租戶，因此客戶託管金鑰可能不再工作。 有關詳細資訊，請參閱在 FAQ 中的 Azure AD 目錄[和 Azure 資源的託管標識的已知問題](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)**之間傳輸訂閱**。  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>在 Azure 金鑰保存庫中存儲客戶管理的金鑰

要啟用客戶管理的金鑰，必須使用 Azure 金鑰保存庫來存儲金鑰。 您必須在金鑰保存庫中同時啟用 **"虛刪除****"和"不清除"** 屬性。

認知服務加密僅支援大小為 2048 的 RSA 金鑰。 有關金鑰的詳細資訊，請參閱["關於 Azure 金鑰保存庫金鑰、機密和證書"](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)中的**金鑰保存庫金鑰**。

### <a name="rotate-customer-managed-keys"></a>旋轉客戶管理的金鑰

您可以根據合規性策略在 Azure 金鑰保存庫中輪換客戶管理的金鑰。 旋轉金鑰時，必須更新認知服務資源才能使用新的金鑰 URI。 要瞭解如何更新資源以在 Azure 門戶中使用金鑰的新版本，請參閱[使用 Azure 門戶在"配置認知服務的客戶管理金鑰"](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-portal.md)中**更新金鑰版本的**部分。

旋轉金鑰不會觸發資源中資料的重新加密。 使用者無需執行進一步操作。

### <a name="revoke-access-to-customer-managed-keys"></a>撤銷對客戶管理金鑰的訪問

要撤銷對客戶管理金鑰的存取權限，請使用 PowerShell 或 Azure CLI。 有關詳細資訊，請參閱[Azure 金鑰保存庫 PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//)或[Azure 金鑰保存庫 CLI](https://docs.microsoft.com/cli/azure/keyvault)。 撤銷訪問可有效地阻止對認知服務資源中的所有資料的訪問，因為認知服務無法訪問加密金鑰。


