---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/07/2020
ms.author: orspodek
ms.openlocfilehash: 7f5c02c6c009e8916ed063454e0ae6049892e95c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297921"
---
Azure 資料資源管理器在靜態存儲帳戶中加密所有資料。 預設情況下，資料使用 Microsoft 管理的金鑰進行加密。 為了對加密金鑰進行其他控制，可以提供客戶管理的金鑰，用於資料加密。 

客戶管理的金鑰必須存儲在[Azure 金鑰保存庫中](/azure/key-vault/key-vault-overview)。 您可以創建自己的金鑰並將其存儲在金鑰保存庫中，也可以使用 Azure 金鑰保存庫 API 生成金鑰。 Azure 資料資源管理器群集和金鑰保存庫必須位於同一區域中，但它們可以位於不同的訂閱中。 有關客戶管理的金鑰的詳細說明，請參閱[使用 Azure 金鑰保存庫的客戶管理金鑰](/azure/storage/common/storage-service-encryption)。 

本文介紹如何配置客戶管理的金鑰。

## <a name="configure-azure-key-vault"></a>設定 Azure Key Vault

要使用 Azure 資料資源管理器配置客戶管理的金鑰，必須在[金鑰保存庫上設置兩個屬性](/azure/key-vault/key-vault-ovw-soft-delete)：**虛刪除**和 **"不清除**"。 預設情況下，這些屬性未啟用。 要啟用這些屬性，請在新金鑰保存庫或現有金鑰保存庫上執行啟用[PowerShell](/azure/key-vault/key-vault-soft-delete-powershell)或[Azure CLI](/azure/key-vault/key-vault-soft-delete-cli)中的**虛刪除**和**啟用清除保護**。 僅支援大小為 2048 的 RSA 金鑰。 有關金鑰的詳細資訊，請參閱[金鑰保存庫金鑰](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)。

> [!NOTE]
> [領導者和跟隨者群集](/azure/data-explorer/follower)不支援使用客戶託管金鑰進行資料加密。 

## <a name="assign-an-identity-to-the-cluster"></a>向群集分配標識

要為群集啟用客戶管理的金鑰，請先為群集分配系統分配的託管標識。 您將使用此託管標識授予訪問金鑰保存庫的群集許可權。 要配置系統分配的託管標識，請參閱[託管標識](/azure/data-explorer/managed-identities)。