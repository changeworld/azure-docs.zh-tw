---
title: 在 Azure 中保護 Azure 資料資源管理器群集
description: 瞭解如何在 Azure 資料資源管理器中保護群集。
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 786950011f10e25d6bcb72061212c1878e79d45a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77373364"
---
# <a name="secure-azure-data-explorer-clusters-in-azure"></a>在 Azure 中保護 Azure 資料資源管理器群集

本文介紹了 Azure 資料資源管理器中的安全性，以説明您保護雲中的資料和資源並滿足企業的安全需求。 確保群集安全非常重要。 保護群集包括一個或多個 Azure 功能，其中包括安全訪問和存儲。 本文提供了説明您確保群集安全的資訊。

## <a name="managed-identities-for-azure-resources"></a>適用於 Azure 資源的受控識別

構建雲應用程式時面臨的一個常見挑戰是代碼中的憑據管理，以便對雲服務進行身份驗證。 保護好這些認證是相當重要的工作。 憑據不應存儲在開發人員工作站中或簽入原始程式碼管理。 Azure Key Vault 可安全地儲存認證、祕密和其他金鑰，但是您的程式碼必須向 Key Vault 進行驗證，才可擷取這些項目。

Azure 資源功能的 Azure 活動目錄 （Azure AD） 託管標識解決了此問題。 這項功能會在 Azure AD 中將自動受控識別提供給 Azure 服務。 您可以使用此身分識別來完成任何支援 Azure AD 驗證的服務驗證 (包括 Key Vault)，不需要您程式碼中的任何認證。 有關此服務的詳細資訊，請參閱 Azure 資源概述頁的[託管標識](/azure/active-directory/managed-identities-azure-resources/overview)。

## <a name="data-encryption"></a>資料加密

### <a name="azure-disk-encryption"></a>Azure 磁碟加密

[Azure 磁片加密](/azure/security/azure-security-disk-encryption-overview)有助於保護資料，以滿足組織安全性和合規性承諾。 它為群集虛擬機器的作業系統和資料磁片提供卷加密。 Azure 磁片加密還與[Azure 金鑰保存庫](/azure/key-vault/)集成，這使我們能夠控制和管理磁片加密金鑰和機密，並確保 VM 磁片上的所有資料都加密。 

### <a name="customer-managed-keys-with-azure-key-vault"></a>使用 Azure 金鑰保存庫的客戶管理金鑰

預設情況下，資料使用 Microsoft 管理的金鑰進行加密。 為了對加密金鑰進行其他控制，可以提供客戶管理的金鑰，用於資料加密。 您可以使用自己的金鑰在存儲級別管理資料的加密。 客戶管理的金鑰用於保護和控制對根加密金鑰的訪問，根加密金鑰用於加密和解密所有資料。 客戶管理的金鑰提供了創建、旋轉、禁用和撤銷存取控制的更大靈活性。 您還可以審核用於保護資料的加密金鑰。

使用 Azure 金鑰保存庫存儲客戶管理的金鑰。 您可以創建自己的金鑰並將其存儲在金鑰保存庫中，也可以使用 Azure 金鑰保存庫 API 生成金鑰。 Azure 資料資源管理器群集和 Azure 金鑰保存庫必須位於同一區域中，但它們可以位於不同的訂閱中。 有關 Azure 金鑰保存庫的詳細資訊，請參閱[什麼是 Azure 金鑰保存庫？](/azure/key-vault/key-vault-overview) 有關客戶管理的金鑰的詳細說明，請參閱[使用 Azure 金鑰保存庫的客戶管理金鑰](/azure/storage/common/storage-service-encryption)。 使用[C#](/azure/data-explorer/customer-managed-keys-csharp)或[Azure 資源管理器範本](/azure/data-explorer/customer-managed-keys-resource-manager)在 Azure 資料資源管理器群集中配置客戶管理金鑰

> [!Note]
> 客戶託管金鑰依賴于 Azure 資源的託管標識，這是 Azure 活動目錄 （Azure AD） 的一項功能。 要在 Azure 門戶中配置客戶託管金鑰，需要將**系統分配的**託管標識配置為群集，如為 Azure[資料資源管理器群集配置託管標識](/azure/data-explorer/managed-identities)中所述。

#### <a name="store-customer-managed-keys-in-azure-key-vault"></a>在 Azure 金鑰保存庫中存儲客戶管理的金鑰

要在群集上啟用客戶管理的金鑰，請使用 Azure 金鑰保存庫來存儲金鑰。 您必須在金鑰保存庫中同時啟用 **"虛刪除****"和"不清除"** 屬性。 金鑰保存庫必須位於與群集相同的訂閱中。 Azure 資料資源管理器使用 Azure 資源的託管標識對金鑰保存庫進行加密和解密操作的身份驗證。 託管標識不支援跨目錄方案。

#### <a name="rotate-customer-managed-keys"></a>旋轉客戶管理的金鑰

您可以根據合規性策略在 Azure 金鑰保存庫中輪換客戶管理的金鑰。 旋轉金鑰時，必須更新群集以使用新金鑰 URI。 旋轉金鑰不會觸發群集中資料的重新加密。 

#### <a name="revoke-access-to-customer-managed-keys"></a>撤銷對客戶管理金鑰的訪問

要撤銷對客戶管理金鑰的存取權限，請使用 PowerShell 或 Azure CLI。 有關詳細資訊，請參閱[Azure 金鑰保存庫 PowerShell](/powershell/module/az.keyvault/)或[Azure 金鑰保存庫 CLI](/cli/azure/keyvault)。 由於 Azure 資料資源管理器無法訪問加密金鑰，因此撤銷訪問將阻止訪問群集存儲級別中的所有資料。

> [!Note]
> 當 Azure 資料資源管理器標識取消對客戶管理金鑰的訪問時，它將自動掛起群集以刪除任何緩存的資料。 返回對金鑰的訪問後，需要手動恢復群集。

## <a name="role-based-access-control"></a>角色型存取控制

使用[基於角色的存取控制 （RBAC），](/azure/role-based-access-control/overview)您可以隔離團隊中的職責，並僅授予群集使用者所需的存取權限。 您只能允許某些操作，而不是向所有人授予群集上的無限制許可權。 可以使用[Azure CLI](/azure/role-based-access-control/role-assignments-cli)或[Azure PowerShell](/azure/role-based-access-control/role-assignments-powershell)配置[Azure 門戶](/azure/role-based-access-control/role-assignments-portal)中的[資料庫的存取控制](/azure/data-explorer/manage-database-permissions)。

## <a name="next-steps"></a>後續步驟

* 通過在靜態啟用加密來保護[Azure 資料資源管理器 - 門戶中的群集](manage-cluster-security.md)。
* [為 Azure 資料資源管理器群集配置託管標識](managed-identities.md)
* [使用 Azure 資源管理器範本配置客戶託管金鑰](customer-managed-keys-resource-manager.md)
* [使用 C 配置客戶託管金鑰#](customer-managed-keys-csharp.md)

