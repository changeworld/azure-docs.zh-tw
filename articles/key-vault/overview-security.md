---
title: Azure 金鑰保存庫安全性 |微軟文檔
description: 針對 Azure Key Vault、金鑰和祕密進行存取權限管理。 包含 Key Vault 的驗證和授權模型以及如何保護金鑰保存庫。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: mbaldwin
Customer intent: As a key vault administrator, I want to learn the options available to secure my vaults
ms.openlocfilehash: 3cc5cb68f430ac8e5070b9c8c4a1aa0c28639311
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270897"
---
# <a name="azure-key-vault-security"></a>Azure 金鑰保存庫安全性

您需要在雲中保護加密金鑰和密碼（如證書、連接字串和密碼）等機密，以便使用 Azure 金鑰保存庫。 由於您正在存儲敏感和業務關鍵型資料，因此您需要採取措施，最大限度地提高保存庫及其中存儲的資料的安全性。 本文將介紹在設計 Azure 金鑰保存庫安全性時應考慮的一些概念。

## <a name="identity-and-access-management"></a>身分識別和存取管理

當您在 Azure 訂用帳戶中建立金鑰保存庫時，它會自動與該訂用帳戶的 Azure AD 租用戶建立關聯。 任何嘗試從保存庫管理或檢索內容的人都必須經過 Azure AD 身份驗證。

- 驗證會建立呼叫者的身分識別。
- 授權確定調用方可以執行哪些操作。 金鑰保存庫中的授權使用[基於角色的存取控制](../role-based-access-control/overview.md)（RBAC） 和 Azure 金鑰保存庫訪問策略的組合。

### <a name="access-model-overview"></a>存取模型概觀

通過兩個介面或平面訪問保存庫。 這些平面是管理平面和資料平面。

- *管理平面*是管理金鑰保存庫本身的位置，它是用於創建和刪除保存庫的介面。 您還可以讀取金鑰保存庫屬性並管理訪問策略。
- *資料平面*允許您處理存儲在金鑰保存庫中的資料。 您可以新增、刪除和修改金鑰、祕密和憑證。

要訪問任一平面中的金鑰保存庫，必須對所有調用方（使用者或應用程式）進行身份驗證和授權。 兩個平面都會使用 Azure Active Directory (Azure AD) 來進行驗證。 至於授權，管理平面會使用角色型存取控制 (RBAC)，資料平面則會使用 Key Vault 存取原則。

這兩個平面的單一驗證機制模型有幾個優點：

- 組織可以集中控制其組織內所有金鑰保存庫的存取權。
- 使用者如果離職，就會立即失去組織中所有金鑰保存庫的存取權。
- 組織可以使用 Azure AD 中的選項自訂身份驗證，例如為增加安全性啟用多重要素驗證

### <a name="managing-administrative-access-to-key-vault"></a>管理對金鑰保存庫的管理訪問

在資源組中創建金鑰保存庫時，可以使用 Azure AD 管理訪問。 您可以對使用者或群組授與在資源群組中管理金鑰保存庫的能力。 您可以通過分配適當的 RBAC 角色來在特定範圍級別授予存取權限。 若要對使用者授與管理金鑰保存庫的權限，您可以在特定範圍對使用者指派預先定義的 `key vault Contributor` 角色。 您可以對 RBAC 角色指派下列範圍層級：

- **訂閱**：在訂閱級別分配的 RBAC 角色應用於該訂閱中的所有資源組和資源。
- **資源組**：在資源組級別分配的 RBAC 角色應用於該資源組中的所有資源。
- **特定資源**：為特定資源配置的 RBAC 角色應用於該資源。 在此情況下，資源會是特定的金鑰保存庫。

有數個預先定義的角色。 如果預先定義的角色不符合您的需求，您可以定義您自己的角色。 有關詳細資訊，請參閱[RBAC：內置角色](../role-based-access-control/built-in-roles.md)。

> [!IMPORTANT]
> 如果使用者具有金鑰保存庫管理平面的 `Contributor` 權限，就可以透過設定 Key Vault 存取原則，對自己授與資料平面的存取權。 因此，請嚴格控制誰可以有金鑰保存庫的 `Contributor` 角色存取權。 請確定只有獲得授權的人員可以存取和管理您的金鑰保存庫、金鑰、祕密和憑證。

<a id="data-plane-access-control"></a>
### <a name="controlling-access-to-key-vault-data"></a>控制對金鑰保存庫資料的訪問

金鑰保存庫訪問策略分別授予金鑰、機密或證書的許可權。 您只能對使用者授與存取金鑰 (而非祕密) 的權限。 金鑰、機密和證書的存取權限在保存庫級別進行管理。

> [!IMPORTANT]
> Key Vault 存取原則不支援細微物件層級的權限，例如特定金鑰、祕密或憑證。 對使用者授與金鑰的建立和刪除權限時，其便可對該金鑰保存庫的所有金鑰執行這些作業。

若要設定金鑰保存庫的存取原則，請使用 [Azure 入口網站](https://portal.azure.com/)、[Azure CLI](../cli-install-nodejs.md)、[Azure PowerShell](/powershell/azureps-cmdlets-docs) 或 [Key Vault 管理 REST API](https://msdn.microsoft.com/library/azure/mt620024.aspx)。

您可以使用 [Azure Key Vault 的虛擬網路服務端點](key-vault-overview-vnet-service-endpoints.md)來限制資料平面的存取。 您可以設定[防火牆和虛擬網路規則](key-vault-network-security.md)來額外加上一層安全性。

## <a name="network-access"></a>網路存取

您可以通過指定可以訪問哪些 IP 位址來減少保存庫的暴露。 Azure Key Vault 的虛擬網路服務端點可讓您將存取權限制為指定的虛擬網路。 這些端點也可讓您將存取權限制為 IPv4 (網際網路通訊協定第 4 版) 位址範圍的清單。 任何從這些來源之外連線到金鑰保存庫的使用者都會被拒絕存取。

防火牆規則生效後，使用者只能在其請求來自允許的虛擬網路或 IPv4 位址範圍時讀取金鑰保存庫的資料。 這也適用於從 Azure 入口網站存取 Key Vault。 雖然使用者可以從 Azure 入口網站瀏覽金鑰保存庫，但是如果其用戶端電腦不在允許的清單中，他們就無法列出金鑰、祕密或憑證。 這也會影響其他 Azure 服務的 [金鑰保存庫選擇器]。 使用者可以看到金鑰保存庫清單，但是如果防火牆規則阻止其用戶端電腦，則不會列出金鑰。

有關 Azure 金鑰保存庫網路位址的詳細資訊，請查看[Azure 金鑰保存庫的虛擬網路服務終結點](key-vault-overview-vnet-service-endpoints.md)

## <a name="monitoring"></a>監視

金鑰保存庫日誌記錄保存有關在保存庫上執行的活動的資訊。 金鑰保存庫日誌：

- 所有經過身份驗證的 REST API 請求，包括失敗的請求
  - 對金鑰保存庫本身的操作。 這些操作包括創建、刪除、設置訪問策略和更新金鑰保存庫屬性（如標記）。
  - 金鑰保存庫中的金鑰和祕密作業，包括：
    - 建立、修改或刪除這些金鑰或祕密。
    - 簽署、驗證、加密、解密、包裝和解除包裝金鑰、取得秘密，以及列出金鑰和祕密 (及其版本)。
- 產生 401 回應的未經驗證要求。 例如，沒有持有人權杖的要求、格式不正確或已過期的要求，或具有無效權杖的要求。

可以在金鑰保存庫操作後的 10 分鐘內訪問日誌記錄資訊。 由您管理存儲帳戶中的日誌。

- 請使用標準的 Azure 存取控制方法限制可存取記錄的人員，藉此來保護記錄。
- 刪除不想繼續保留在儲存體帳戶中的記錄。

有關安全管理存儲帳戶的建議，請查看[Azure 存儲安全指南](../storage/blobs/security-recommendations.md)

## <a name="next-steps"></a>後續步驟

- [Azure Key Vault 的虛擬網路服務端點](key-vault-overview-vnet-service-endpoints.md)
- [RBAC：內建角色](../role-based-access-control/built-in-roles.md)
- [Azure 金鑰保存庫的虛擬網路服務終結點](key-vault-overview-vnet-service-endpoints.md)
